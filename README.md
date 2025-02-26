<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PulseEditor</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto|Open+Sans|Montserrat|Lora|Poppins|...&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="styles.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
</head>
<body>
    <div id="container">
        <!-- Barra de Ferramentas -->
        <div id="toolbar">
            <select id="fontSelect" onchange="changeFont()">
                <option value="Roboto">Roboto</option>
                <option value="Open Sans">Open Sans</option>
                <option value="Montserrat">Montserrat</option>
                <option value="Lora">Lora</option>
                <option value="Poppins">Poppins</option>
                <!-- Adicione mais 10 fontes -->
            </select>
            <button onclick="document.execCommand('bold')">Negrito</button>
            <button onclick="document.execCommand('italic')">Itálico</button>
            <button onclick="document.execCommand('underline')">Sublinhado</button>
            <button onclick="insertTable(3, 3)">Inserir Tabela</button>
            <button onclick="generatePDF()">Salvar PDF</button>
            <button onclick="saveAsTxt()">Salvar .txt</button>
            <button onclick="saveAsDocx()">Salvar .docx</button>
            <button onclick="toggleZenMode()">Modo Zen</button>
            <select id="themeSelect" onchange="changeTheme()">
                <option value="dark">Escuro</option>
                <option value="light">Claro</option>
                <option value="blue">Azul Escuro</option>
                <option value="green">Verde Escuro</option>
            </select>
            <input type="file" id="bgImage" accept="image/*" onchange="changeBackground(event)">
            <button onclick="playMusic()">Tocar Música</button>
            <button onclick="pauseMusic()">Pausar Música</button>
            <button onclick="startPomodoro()">Iniciar Pomodoro</button>
            <button onclick="toggleBudgetPanel()">Orçamento</button>
        </div>

        <!-- Editor -->
        <div id="editor" contenteditable="true" onload="loadEditor()"></div>

        <!-- Música -->
        <audio id="ambientMusic" loop>
            <source src="assets/ambient.mp3" type="audio/mp3">
        </audio>

        <!-- Menu de Orçamento -->
        <div id="budgetPanel">
            <h3>PulseEditor - Orçamento</h3>

            <!-- Logo e Dados da Empresa -->
            <input type="file" id="companyLogo" accept="image/*" onchange="uploadLogo(event)">
            <img id="logoPreview" src="" alt="Logo" style="max-width: 100px; display: none;">
            <input id="companyName" placeholder="Nome da Empresa">
            <input id="companyAddress" placeholder="Endereço da Empresa">
            <input id="companyDate" type="date">

            <!-- Registro de Cliente -->
            <h4>Cliente</h4>
            <input id="clientName" placeholder="Nome do Cliente">
            <input id="clientAddress" placeholder="Endereço do Cliente">
            <input id="clientPhone" placeholder="Telefone">
            <input id="clientEmail" placeholder="E-mail">

            <!-- Registro de Fornecedor -->
            <h4>Fornecedor</h4>
            <input id="supplierName" placeholder="Nome do Fornecedor">
            <input id="supplierAddress" placeholder="Endereço do Fornecedor">

            <!-- Itens do Orçamento -->
            <h4>Itens</h4>
            <input id="itemName" placeholder="Item">
            <input id="itemDescription" placeholder="Descrição">
            <input id="itemUnitValue" type="number" placeholder="Valor Unitário">
            <input id="itemQuantity" type="number" placeholder="Quantidade">
            <button onclick="addItem()">Adicionar Item</button>
            <div id="itemList"></div>
            <p>Total: <span id="totalValue">0.00</span></p>

            <!-- Rodapé -->
            <textarea id="footerInfo" placeholder="Informações do Rodapé (ex.: Condições de Pagamento)"></textarea>

            <!-- Encaminhamento -->
            <h4>Encaminhar</h4>
            <input id="sendToEmail" placeholder="E-mail de Destino">
            <input id="sendToWhatsApp" placeholder="Número WhatsApp (ex.: +5511999999999)">
            <button onclick="sendBudget('email')">Enviar por E-mail</button>
            <button onclick="sendBudget('whatsapp')">Enviar por WhatsApp</button>

            <button onclick="saveBudget()">Salvar Orçamento</button>
        </div>

        <!-- Cronômetro Pomodoro -->
        <div id="pomodoroTimer">25:00</div>
    </div>
    <script src="script.js"></script>
</body>
</html>body {
    background: #1a1a1a url('assets/background.jpg') no-repeat center/cover;
    color: #fff;
    font-family: 'Roboto', sans-serif;
    margin: 0;
    padding: 0;
    min-height: 100vh;
    transition: background 0.3s, color 0.3s;
}

#container {
    display: flex;
    flex-direction: column;
    height: 100vh;
}

#toolbar {
    background: rgba(0, 0, 0, 0.8);
    padding: 10px;
    display: flex;
    gap: 10px;
}

button {
    border: 1px solid white;
    background: transparent;
    color: white;
    padding: 5px 10px;
    cursor: pointer;
    transition: background 0.3s;
}

button:hover {
    background: rgba(255, 255, 255, 0.2);
}

#editor {
    flex: 1;
    padding: 20px;
    background: rgba(255, 255, 255, 0.1);
    outline: none;
    overflow-y: auto;
}

#budgetPanel {
    width: 350px;
    position: fixed;
    right: -350px; /* Escondido por padrão */
    top: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.9);
    padding: 20px;
    transition: right 0.3s;
    overflow-y: auto;
}

#budgetPanel.active {
    right: 0; /* Visível quando ativo */
}

#budgetPanel input, #budgetPanel textarea {
    width: 100%;
    margin: 5px 0;
    padding: 5px;
    background: #333;
    border: 1px solid #fff;
    color: #fff;
}

#itemList {
    margin: 10px 0;
}

#pomodoroTimer {
    position: fixed;
    bottom: 10px;
    left: 10px;
    font-size: 24px;
    display: none;
}

/* Temas */
.light { background: #fff; color: #000; }
.blue { background: #1a2a44; color: #fff; }
.green { background: #1a3c34; color: #fff; }

/* Modo Zen */
.zen #toolbar, .zen #budgetPanel, .zen #pomodoroTimer { display: none; }
.zen #editor { background: transparent; }// Editor
function changeFont() {
    const font = document.getElementById('fontSelect').value;
    document.getElementById('editor').style.fontFamily = font;
}

function saveEditor() {
    localStorage.setItem('pulseEditorContent', document.getElementById('editor').innerHTML);
}

function loadEditor() {
    document.getElementById('editor').innerHTML = localStorage.getItem('pulseEditorContent') || '';
}

// Música
function playMusic() {
    document.getElementById('ambientMusic').play();
}

function pauseMusic() {
    document.getElementById('ambientMusic').pause();
}

// Imagem de Fundo
function changeBackground(event) {
    const file = event.target.files[0];
    const url = URL.createObjectURL(file);
    document.body.style.backgroundImage = `url(${url})`;
}

// Tabela
function insertTable(rows, cols) {
    let table = '<table border="1">';
    for (let i = 0; i < rows; i++) {
        table += '<tr>';
        for (let j = 0; j < cols; j++) {
            table += '<td contenteditable="true"></td>';
        }
        table += '</tr>';
    }
    table += '</table>';
    document.getElementById('editor').innerHTML += table;
    saveEditor();
}

// Exportação
const { jsPDF } = window.jspdf;
function generatePDF() {
    const doc = new jsPDF();
    doc.text(document.getElementById('editor').innerText, 10, 10);
    doc.save('PulseEditor_documento.pdf');
}

function saveAsTxt() {
    const text = document.getElementById('editor').innerText;
    const blob = new Blob([text], { type: 'text/plain' });
    saveAs(blob, 'PulseEditor_documento.txt');
}

function saveAsDocx() {
    const text = document.getElementById('editor').innerHTML;
    const blob = new Blob([text], { type: 'application/msword' });
    saveAs(blob, 'PulseEditor_documento.docx');
}

// Orçamento
function toggleBudgetPanel() {
    document.getElementById('budgetPanel').classList.toggle('active');
}

function uploadLogo(event) {
    const file = event.target.files[0];
    const url = URL.createObjectURL(file);
    const logo = document.getElementById('logoPreview');
    logo.src = url;
    logo.style.display = 'block';
    localStorage.setItem('pulseEditorLogo', url);
}

let items = [];
function addItem() {
    const item = {
        name: document.getElementById('itemName').value,
        description: document.getElementById('itemDescription').value,
        unitValue: parseFloat(document.getElementById('itemUnitValue').value) || 0,
        quantity: parseInt(document.getElementById('itemQuantity').value) || 1,
    };
    item.total = item.unitValue * item.quantity;
    items.push(item);
    updateItemList();
    updateTotal();
}

function updateItemList() {
    const list = document.getElementById('itemList');
    list.innerHTML = items.map((item, index) => `
        <div>
            <p>${item.name} - ${item.description} | ${item.quantity}x $${item.unitValue.toFixed(2)} = $${item.total.toFixed(2)}</p>
            <button onclick="items.splice(${index}, 1); updateItemList(); updateTotal();">Remover</button>
        </div>
    `).join('');
}

function updateTotal() {
    const total = items.reduce((sum, item) => sum + item.total, 0);
    document.getElementById('totalValue').innerText = total.toFixed(2);
}

function saveBudget() {
    const budget = {
        company: {
            name: document.getElementById('companyName').value,
            address: document.getElementById('companyAddress').value,
            date: document.getElementById('companyDate').value,
            logo: document.getElementById('logoPreview').src,
        },
        client: {
            name: document.getElementById('clientName').value,
            address: document.getElementById('clientAddress').value,
            phone: document.getElementById('clientPhone').value,
            email: document.getElementById('clientEmail').value,
        },
        supplier: {
            name: document.getElementById('supplierName').value,
            address: document.getElementById('supplierAddress').value,
        },
        items: items,
        footer: document.getElementById('footerInfo').value,
    };
    localStorage.setItem('pulseEditorBudget', JSON.stringify(budget));
}

function sendBudget(method) {
    const budget = JSON.parse(localStorage.getItem('pulseEditorBudget') || '{}');
    const text = `
        Orçamento - PulseEditor
        Empresa: ${budget.company?.name || ''} | ${budget.company?.address || ''} | ${budget.company?.date || ''}
        Cliente: ${budget.client?.name || ''} | ${budget.client?.address || ''} | ${budget.client?.phone || ''} | ${budget.client?.email || ''}
        Fornecedor: ${budget.supplier?.name || ''} | ${budget.supplier?.address || ''}
        Itens:
        ${budget.items?.map(i => `${i.name} - ${i.description}: ${i.quantity}x $${i.unitValue.toFixed(2)} = $${i.total.toFixed(2)}`).join('\n')}
        Total: $${budget.items?.reduce((sum, i) => sum + i.total, 0).toFixed(2) || '0.00'}
        Rodapé: ${budget.footer || ''}
    `;
    if (method === 'email') {
        const email = document.getElementById('sendToEmail').value;
        window.open(`mailto:${email}?subject=Orçamento PulseEditor&body=${encodeURIComponent(text)}`);
    } else if (method === 'whatsapp') {
        const phone = document.getElementById('sendToWhatsApp').value;
        window.open(`https://wa.me/${phone}?text=${encodeURIComponent(text)}`);
    }
}

// Modo Zen
function toggleZenMode() {
    document.body.classList.toggle('zen');
}

// Temas
function changeTheme() {
    const theme = document.getElementById('themeSelect').value;
    document.body.className = theme;
}

// Cronômetro Pomodoro
let timeLeft = 25 * 60;
let timerId = null;
function startPomodoro() {
    document.getElementById('pomodoroTimer').style.display = 'block';
    if (timerId) clearInterval(timerId);
    timerId = setInterval(() => {
        timeLeft--;
        const minutes = Math.floor(timeLeft / 60);
        const seconds = timeLeft % 60;
        document.getElementById('pomodoroTimer').innerText = `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
        if (timeLeft <= 0) {
            clearInterval(timerId);
            alert('Pomodoro concluído pelo PulseEditor!');
            timeLeft = 25 * 60;
        }
    }, 1000);
}

// Carregar conteúdo ao iniciar
window.onload = function() {
    loadEditor();
    const savedLogo = localStorage.getItem('pulseEditorLogo');
    if (savedLogo) {
        document.getElementById('logoPreview').src = savedLogo;
        document.getElementById('logoPreview').style.display = 'block';
    }
};function previewBudget() {
    const budget = JSON.parse(localStorage.getItem('pulseEditorBudget') || '{}');
    const text = `
        <h2>Orçamento - ${budget.company?.name || 'PulseEditor'}</h2>
        <img src="${budget.company?.logo || ''}" style="max-width: 100px;">
        <p>Endereço: ${budget.company?.address || ''} | Data: ${budget.company?.date || ''}</p>
        <h3>Cliente</h3>
        <p>${budget.client?.name || ''}<br>${budget.client?.address || ''}<br>${budget.client?.phone || ''} | ${budget.client?.email || ''}</p>
        <h3>Fornecedor</h3>
        <p>${budget.supplier?.name || ''}<br>${budget.supplier?.address || ''}</p>
        <h3>Itens</h3>
        <table border="1">
            <tr><th>Item</th><th>Descrição</th><th>Quantidade</th><th>Valor Unitário</th><th>Total</th></tr>
            ${budget.items?.map(i => `<tr><td>${i.name}</td><td>${i.description}</td><td>${i.quantity}</td><td>$${i.unitValue.toFixed(2)}</td><td>$${i.total.toFixed(2)}</td></tr>`).join('')}
        </table>
        <p><strong>Total: $${budget.items?.reduce((sum, i) => sum + i.total, 0).toFixed(2) || '0.00'}</strong></p>
        <p>${budget.footer || ''}</p>
    `;
    document.getElementById('editor').innerHTML = text;
    saveEditor();
}function generateBudgetPDF() {
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    const budget = JSON.parse(localStorage.getItem('pulseEditorBudget') || '{}');
    let y = 10;
    doc.text(`Orçamento - ${budget.company?.name || 'PulseEditor'}`, 10, y);
    y += 10;
    if (budget.company?.logo) doc.addImage(budget.company.logo, 'JPEG', 10, y, 50, 20);
    y += 30;
    doc.text(`Endereço: ${budget.company?.address || ''} | Data: ${budget.company?.date || ''}`, 10, y);
    y += 10;
    doc.text(`Cliente: ${budget.client?.name || ''}, ${budget.client?.address || ''}`, 10, y);
    y += 10;
    doc.text(`Fornecedor: ${budget.supplier?.name || ''}, ${budget.supplier?.address || ''}`, 10, y);
    y += 10;
    doc.text('Itens:', 10, y);
    y += 10;
    budget.items?.forEach((item, i) => {
        doc.text(`${i + 1}. ${item.name} - ${item.description}: ${item.quantity}x $${item.unitValue.toFixed(2)} = $${item.total.toFixed(2)}`, 10, y);
        y += 10;
    });
    doc.text(`Total: $${budget.items?.reduce((sum, i) => sum + i.total, 0).toFixed(2) || '0.00'}`, 10, y);
    y += 10;
    doc.text(budget.footer || '', 10, y);
    doc.save('PulseEditor_orcamento.pdf');
}
