<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Складской сканер</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: #f5f5f5;
            margin: 0;
            padding: 0;
            padding-bottom: 80px;
            width: 100%;
            overflow-x: hidden;
        }
        
        .container {
            max-width: 100%;
            width: 100%;
            padding: 12px;
            margin: 0 auto;
        }
        
        .card {
            background: white;
            border-radius: 16px;
            padding: 15px;
            margin-bottom: 12px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
        
        .header {
            text-align: center;
            margin-bottom: 15px;
        }
        
        .header h1 {
            font-size: 22px;
            margin: 0;
            color: #333;
        }
        
        .status {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 12px;
            margin-top: 8px;
        }
        
        .status.online {
            background: #d4edda;
            color: #155724;
        }
        
        .status.offline {
            background: #f8d7da;
            color: #721c24;
        }
        
        .status.checking {
            background: #fff3cd;
            color: #856404;
        }
        
        .scanner-section {
            text-align: center;
        }
        
        video {
            width: 100%;
            border-radius: 12px;
            background: #000;
            margin-top: 8px;
        }
        
        .btn {
            width: 100%;
            padding: 14px;
            font-size: 16px;
            font-weight: bold;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            margin-top: 10px;
            transition: transform 0.1s;
        }
        
        .btn:active {
            transform: scale(0.97);
        }
        
        .btn-primary {
            background: #007aff;
            color: white;
        }
        
        .btn-success {
            background: #34c759;
            color: white;
        }
        
        .btn-secondary {
            background: #8e8e93;
            color: white;
        }
        
        .btn-danger {
            background: #ff3b30;
            color: white;
        }
        
        .btn-warning {
            background: #ff9800;
            color: white;
        }
        
        .input-group {
            margin-bottom: 15px;
        }
        
        .input-group label {
            display: block;
            font-size: 14px;
            font-weight: 600;
            margin-bottom: 6px;
        }
        
        .input-group input {
            width: 100%;
            padding: 12px;
            font-size: 16px;
            border: 2px solid #e0e0e0;
            border-radius: 12px;
            background: white;
        }
        
        .cart-item {
            background: #f9f9f9;
            border-radius: 12px;
            padding: 12px;
            margin-bottom: 8px;
        }
        
        .cart-item-info {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 8px;
        }
        
        .cart-item-name {
            font-weight: bold;
            font-size: 15px;
            flex: 1;
        }
        
        .cart-item-ozm {
            font-size: 11px;
            color: #666;
            margin-top: 4px;
        }
        
        .cart-item-qty {
            font-size: 16px;
            font-weight: bold;
            color: #007aff;
        }
        
        .cart-item-actions {
            display: flex;
            justify-content: flex-end;
            align-items: center;
            gap: 10px;
            margin-top: 8px;
        }
        
        .cart-item-actions button {
            padding: 6px 15px;
            font-size: 14px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
        }
        
        .btn-minus {
            background: #ff9800;
            color: white;
        }
        
        .btn-plus {
            background: #4caf50;
            color: white;
        }
        
        .btn-delete {
            background: #ff3b30;
            color: white;
        }
        
        .total-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding-top: 12px;
            margin-top: 12px;
            border-top: 2px solid #e0e0e0;
            font-weight: bold;
            font-size: 16px;
        }
        
        .hidden {
            display: none;
        }
        
        .error-message, .success-message {
            padding: 12px;
            border-radius: 12px;
            margin-bottom: 12px;
            text-align: center;
            font-size: 14px;
        }
        
        .error-message {
            background: #f8d7da;
            color: #721c24;
        }
        
        .success-message {
            background: #d4edda;
            color: #155724;
        }
        
        h3 {
            font-size: 18px;
            margin-bottom: 12px;
        }
    </style>
</head>
<body>
<div class="container">
    <div class="header">
        <h1>📦 Складской сканер</h1>
        <div class="status checking" id="status">🔄 Проверка подключения...</div>
    </div>
    
    <div id="errorMessage" class="error-message hidden"></div>
    <div id="successMessage" class="success-message hidden"></div>
    
    <div id="scanMode">
        <div class="card scanner-section">
            <button class="btn btn-primary" id="startCameraBtn">📷 Включить камеру</button>
            <video id="video" playsinline autoplay style="display: none;"></video>
            <button class="btn btn-secondary" id="stopCameraBtn" style="display: none;">⏹️ Остановить</button>
        </div>
        
        <div class="card">
            <div class="input-group">
                <label>🔢 Или введите ОЗМ вручную:</label>
                <input type="text" id="manualOzm" placeholder="Введите штрих-код">
                <button class="btn btn-primary" id="manualSubmitBtn" style="margin-top: 8px;">🔍 Найти и добавить 1 шт</button>
            </div>
        </div>
    </div>
    
    <div id="cartSection" class="card">
        <h3>🛒 Корзина</h3>
        <div id="cartItems"><div style="text-align: center; color: #999; padding: 20px;">Корзина пуста</div></div>
        <div class="total-row hidden" id="totalRow">
            <span>📊 Всего позиций:</span>
            <span id="totalCount">0</span>
        </div>
        <button class="btn btn-success" id="checkoutBtn" style="margin-top: 16px;">✅ Завершить выдачу</button>
    </div>
</div>

<script src="https://unpkg.com/@zxing/library@0.19.2/umd/index.min.js"></script>
<script>
// ============= НАСТРОЙКИ =============
// ⚠️ ЗАМЕНИТЕ ЭТОТ АДРЕС НА ВАШ АДРЕС ИЗ CLOUDPUB!
const SERVER_URL = 'https://diffidently-stalwart-pademelon.cloudpub.ru';

let cart = [];
let scanner = null;
let videoElement = null;

async function checkConnection() {
    const statusDiv = document.getElementById('status');
    statusDiv.textContent = '🔄 Проверка подключения...';
    statusDiv.className = 'status checking';
    
    try {
        const response = await fetch(`${SERVER_URL}/status`, { method: 'GET' });
        const data = await response.json();
        
        if (data.status === 'running') {
            statusDiv.textContent = '✅ Подключено к серверу';
            statusDiv.className = 'status online';
            return true;
        }
    } catch(e) {
        console.log('Connection error:', e);
    }
    
    statusDiv.textContent = '❌ Отсутствует связь с сервером';
    statusDiv.className = 'status offline';
    return false;
}

async function getMaterialInfo(ozm) {
    try {
        const response = await fetch(`${SERVER_URL}/material/${encodeURIComponent(ozm)}`);
        if (response.status === 404) {
            const error = await response.json();
            return { found: false, error: error.error || 'Материал не найден' };
        }
        const data = await response.json();
        return data;
    } catch(e) {
        return { found: false, error: 'Ошибка соединения' };
    }
}

async function getPersonsList() {
    try {
        const response = await fetch(`${SERVER_URL}/persons`);
        const data = await response.json();
        return data.persons || [];
    } catch(e) {
        return [];
    }
}

async function completeCheckout(person, items) {
    try {
        const response = await fetch(`${SERVER_URL}/checkout`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ person, items })
        });
        return await response.json();
    } catch(e) {
        return { status: 'error', message: 'Ошибка соединения' };
    }
}

function showMessage(elementId, text) {
    const el = document.getElementById(elementId);
    el.textContent = text;
    el.classList.remove('hidden');
    setTimeout(() => el.classList.add('hidden'), 3000);
}

// --- Инициализация ---
checkConnection();
setInterval(checkConnection, 30000);

// --- Сканер ---
document.getElementById('startCameraBtn').addEventListener('click', startScanner);
document.getElementById('stopCameraBtn').addEventListener('click', stopScanner);

function startScanner() {
    if (scanner) stopScanner();
    const video = document.getElementById('video');
    videoElement = video;
    
    navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } })
        .then(stream => {
            video.srcObject = stream;
            video.style.display = 'block';
            video.play();
            document.getElementById('startCameraBtn').style.display = 'none';
            document.getElementById('stopCameraBtn').style.display = 'block';
            
            scanner = new window.ZXing.BrowserMultiFormatReader();
            scanner.decodeFromVideoDevice(null, video, (result, err) => {
                if (result) {
                    const barcode = result.getText();
                    addToCart(barcode, 1);
                }
            });
        })
        .catch(err => showMessage('errorMessage', '❌ Нет доступа к камере'));
}

function stopScanner() {
    if (scanner) {
        scanner.reset();
        scanner = null;
    }
    if (videoElement && videoElement.srcObject) {
        videoElement.srcObject.getTracks().forEach(track => track.stop());
        videoElement.srcObject = null;
    }
    document.getElementById('startCameraBtn').style.display = 'block';
    document.getElementById('stopCameraBtn').style.display = 'none';
    if (videoElement) videoElement.style.display = 'none';
}

// --- Добавление в корзину ---
async function addToCart(ozm, qty = 1) {
    const info = await getMaterialInfo(ozm);
    if (!info.found) {
        showMessage('errorMessage', info.error || 'Материал не найден!');
        return false;
    }
    
    const existingIndex = cart.findIndex(item => item.ozm === ozm);
    if (existingIndex >= 0) {
        cart[existingIndex].qty += qty;
    } else {
        cart.push({ ozm, name: info.name, qty: qty });
    }
    
    updateCartDisplay();
    showMessage('successMessage', `✅ "${info.name}" добавлен (${qty} шт.)`);
    return true;
}

// --- Ручной ввод ---
document.getElementById('manualSubmitBtn').addEventListener('click', async () => {
    const ozm = document.getElementById('manualOzm').value.trim();
    if (!ozm) {
        showMessage('errorMessage', 'Введите ОЗМ');
        return;
    }
    document.getElementById('manualOzm').value = '';
    await addToCart(ozm, 1);
});

document.getElementById('manualOzm').addEventListener('keypress', (e) => {
    if (e.key === 'Enter') document.getElementById('manualSubmitBtn').click();
});

// --- Обновление корзины ---
function updateCartDisplay() {
    const container = document.getElementById('cartItems');
    const totalRow = document.getElementById('totalRow');
    const totalCountSpan = document.getElementById('totalCount');
    
    if (cart.length === 0) {
        container.innerHTML = '<div style="text-align: center; color: #999; padding: 20px;">Корзина пуста</div>';
        totalRow.classList.add('hidden');
        return;
    }
    
    let html = '';
    let totalItems = 0;
    cart.forEach((item, index) => {
        totalItems += item.qty;
        html += `<div class="cart-item">
            <div class="cart-item-info">
                <div class="cart-item-name">${escapeHtml(item.name)}</div>
                <div class="cart-item-ozm">${escapeHtml(item.ozm)}</div>
            </div>
            <div style="display: flex; justify-content: space-between; align-items: center;">
                <div class="cart-item-qty">${item.qty} шт.</div>
                <div class="cart-item-actions">
                    <button class="btn-minus" data-index="${index}">-1</button>
                    <button class="btn-plus" data-index="${index}">+1</button>
                    <button class="btn-delete" data-index="${index}">Удалить</button>
                </div>
            </div>
        </div>`;
    });
    
    container.innerHTML = html;
    totalRow.classList.remove('hidden');
    totalCountSpan.textContent = totalItems;
    
    document.querySelectorAll('.btn-minus').forEach(btn => {
        btn.addEventListener('click', (e) => {
            e.stopPropagation();
            const index = parseInt(btn.dataset.index);
            if (cart[index].qty > 1) {
                cart[index].qty -= 1;
            } else {
                cart.splice(index, 1);
            }
            updateCartDisplay();
        });
    });
    
    document.querySelectorAll('.btn-plus').forEach(btn => {
        btn.addEventListener('click', (e) => {
            e.stopPropagation();
            const index = parseInt(btn.dataset.index);
            cart[index].qty += 1;
            updateCartDisplay();
        });
    });
    
    document.querySelectorAll('.btn-delete').forEach(btn => {
        btn.addEventListener('click', (e) => {
            e.stopPropagation();
            const index = parseInt(btn.dataset.index);
            cart.splice(index, 1);
            updateCartDisplay();
        });
    });
}

// --- Оформление выдачи ---
document.getElementById('checkoutBtn').addEventListener('click', async () => {
    if (cart.length === 0) {
        showMessage('errorMessage', 'Корзина пуста!');
        return;
    }
    
    const persons = await getPersonsList();
    if (!persons.length) {
        showMessage('errorMessage', 'Не удалось загрузить список получателей');
        return;
    }
    
    const modal = document.createElement('div');
    modal.style.cssText = 'position: fixed; top:0; left:0; right:0; bottom:0; background:rgba(0,0,0,0.5); display:flex; justify-content:center; align-items:center; z-index:1000;';
    modal.innerHTML = `<div style="background:white; border-radius:20px; padding:20px; max-width:320px; width:90%; text-align:center;">
        <h3 style="margin:0 0 16px 0;">👤 Кто берёт материалы?</h3>
        <div style="max-height:400px; overflow-y:auto;">
            ${persons.map(p => `<button class="btn btn-primary" style="margin:8px 0; padding:12px; width:100%;" data-person="${p}">${p}</button>`).join('')}
        </div>
        <button class="btn btn-secondary" id="closePersonModal" style="margin-top:12px;">Отмена</button>
    </div>`;
    
    document.body.appendChild(modal);
    
    modal.querySelectorAll('[data-person]').forEach(btn => {
        btn.addEventListener('click', async () => {
            const person = btn.dataset.person;
            modal.remove();
            await processCheckout(person);
        });
    });
    
    modal.querySelector('#closePersonModal')?.addEventListener('click', () => modal.remove());
});

async function processCheckout(person) {
    const btn = document.getElementById('checkoutBtn');
    const originalText = btn.textContent;
    btn.textContent = '⏳ Отправка...';
    btn.disabled = true;
    
    const itemsToSend = cart.map(item => ({ ozm: item.ozm, name: item.name, qty: item.qty }));
    const result = await completeCheckout(person, itemsToSend);
    
    if (result.status === 'success') {
        showMessage('successMessage', `✅ Выдача оформлена!\n👤 Получатель: ${person}\n📊 Позиций: ${cart.length}`);
        cart = [];
        updateCartDisplay();
    } else {
        showMessage('errorMessage', `❌ Ошибка: ${result.message}`);
    }
    
    btn.textContent = originalText;
    btn.disabled = false;
}

function escapeHtml(text) {
    if (!text) return '';
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
}
</script>
</body>
</html>
