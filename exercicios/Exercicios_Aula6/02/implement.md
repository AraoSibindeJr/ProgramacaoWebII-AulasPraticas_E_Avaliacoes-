function validateEmail() {
const val = emailInput.value.trim();
const isValid = val !== "" && val.includes("@");
setFieldStyle(emailInput, isValid);
emailError.textContent = isValid ? "" : 'Email deve conter "@".';
return isValid;
}

function validateIdade() {
const val = idadeInput.value.trim();
if (val === "") {
setFieldStyle(idadeInput, false);
idadeError.textContent = "Idade é obrigatória.";
return false;
}
const num = Number(val);
const isValid = Number.isInteger(num) && num >= 18 && num <= 130;
setFieldStyle(idadeInput, isValid);
idadeError.textContent = isValid ? "" : "Idade deve ser ≥ 18 e ≤ 130.";
return isValid;
}

function validateSenha() {
const val = senhaInput.value;
const isValid = val.length >= 6;
setFieldStyle(senhaInput, isValid);
senhaError.textContent = isValid
? ""
: "Senha deve ter pelo menos 6 caracteres.";
return isValid;
}

function setFieldStyle(input, isValid) {
input.classList.remove("valid", "invalid");
if (isValid) {
input.classList.add("valid");
} else {
input.classList.add("invalid");
}
}

function validateAll() {
const nomeOk = validateNome();
const emailOk = validateEmail();
const idadeOk = validateIdade();
const senhaOk = validateSenha();
return nomeOk && emailOk && idadeOk && senhaOk;
}

function renderClientList() {
const items = clientList.querySelectorAll("li:not(#emptyMessage)");
for (const li of items) {
li.remove();
}

    if (clients.length === 0) {
      if (!emptyMessage.parentNode) {
        clientList.appendChild(emptyMessage);
      }
      emptyMessage.style.display = "block";
    } else {
      if (emptyMessage.parentNode) {
        emptyMessage.style.display = "none";
      }

      for (let i = 0; i < clients.length; i++) {
        const c = clients[i];
        const li = document.createElement("li");

        // nome e idade
        const nameSpan = document.createElement("span");
        nameSpan.className = "client-name";
        nameSpan.textContent = c.nome;

        const ageSpan = document.createElement("span");
        ageSpan.className = "client-age";
        ageSpan.textContent = `${c.idade} anos`;

        // email
        const emailSpan = document.createElement("span");
        emailSpan.className = "client-email";
        emailSpan.textContent = c.email;

        // montagem: agrupa em um container flex
        const leftGroup = document.createElement("span");
        leftGroup.style.display = "flex";
        leftGroup.style.alignItems = "center";
        leftGroup.style.gap = "0.6rem";
        leftGroup.style.flexWrap = "wrap";
        leftGroup.appendChild(nameSpan);
        leftGroup.appendChild(ageSpan);

        const rightGroup = document.createElement("span");
        rightGroup.style.display = "flex";
        rightGroup.style.alignItems = "center";
        rightGroup.appendChild(emailSpan);

        li.appendChild(leftGroup);
        li.appendChild(rightGroup);
        clientList.appendChild(li);
      }
    }

    // atualiza contador
    contadorClientes.textContent = clients.length;

}

// --- adicionar cliente (validado) ---
function addClient() {
// executa validação completa
if (!validateAll()) {
// foca no primeiro campo inválido
const firstInvalid = document.querySelector(".field-group input.invalid");
if (firstInvalid) firstInvalid.focus();
return;
}

    // coleta dados
    const nome = nomeInput.value.trim();
    const email = emailInput.value.trim();
    const idade = Number(idadeInput.value.trim());
    const senha = senhaInput.value; // não armazenamos, só validamos

    // cria objeto e adiciona ao array
    clients.push({ nome, email, idade });

    // limpa campos (mantém foco no nome)
    nomeInput.value = "";
    emailInput.value = "";
    idadeInput.value = "";
    senhaInput.value = "";

    // remove classes de validação dos campos (reset visual)
    [nomeInput, emailInput, idadeInput, senhaInput].forEach((inp) => {
      inp.classList.remove("valid", "invalid");
    });
    // limpa mensagens de erro
    nomeError.textContent = "";
    emailError.textContent = "";
    idadeError.textContent = "";
    senhaError.textContent = "";

    // atualiza a lista
    renderClientList();

    // foco no nome para nova entrada
    nomeInput.focus();

}

// --- limpar todos os clientes ---
function clearAllClients() {
if (clients.length === 0) return;
if (confirm("Remover todos os clientes cadastrados?")) {
clients = [];
renderClientList();
// reseta campos e estilos
[nomeInput, emailInput, idadeInput, senhaInput].forEach((inp) => {
inp.value = "";
inp.classList.remove("valid", "invalid");
});
nomeError.textContent = "";
emailError.textContent = "";
idadeError.textContent = "";
senhaError.textContent = "";
nomeInput.focus();
}
}

// --- eventos ---

// submit do formulário (com preventDefault)
form.addEventListener("submit", function (e) {
e.preventDefault();
addClient();
});

// validação em tempo real (ao perder o foco ou input) para feedback visual
nomeInput.addEventListener("blur", validateNome);
emailInput.addEventListener("blur", validateEmail);
idadeInput.addEventListener("blur", validateIdade);
senhaInput.addEventListener("blur", validateSenha);

// também ao digitar (para remover estado inválido enquanto corrige)
nomeInput.addEventListener("input", function () {
if (this.classList.contains("invalid")) validateNome();
});
emailInput.addEventListener("input", function () {
if (this.classList.contains("invalid")) validateEmail();
});
idadeInput.addEventListener("input", function () {
if (this.classList.contains("invalid")) validateIdade();
});
senhaInput.addEventListener("input", function () {
if (this.classList.contains("invalid")) validateSenha();
});

// botão limpar
limparBtn.addEventListener("click", clearAllClients);

// --- inicialização: dados de exemplo (para demonstrar) ---
// (comentado para começar vazio, mas pode ativar)
// clients = [
// { nome: 'João Alberto', email: 'joao@carpintaria.com', idade: 52 },
// { nome: 'Maria Silva', email: 'maria@email.com', idade: 34 }
// ];
// renderClientList();

// Inicia vazio, mas com a mensagem visível
renderClientList();

// Adiciona um pequeno ajuste: se clicar em cadastrar com campos vazios, mostra erros
// já está coberto pelo validateAll().
})();
