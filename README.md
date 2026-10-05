// ===== NAVBAR SCROLL =====
const navbar = document.getElementById('navbar');
window.addEventListener('scroll', () => {
    if (window.scrollY > 50) {
        navbar.classList.add('scrolled');
    } else {
        navbar.classList.remove('scrolled');
    }
});

// ===== MENU HAMBURGER =====
const hamburger = document.getElementById('hamburger');
const navMenu = document.getElementById('navMenu');

hamburger.addEventListener('click', () => {
    hamburger.classList.toggle('active');
    navMenu.classList.toggle('active');
});

// Fechar menu ao clicar em link
document.querySelectorAll('.nav-link').forEach(link => {
    link.addEventListener('click', () => {
        hamburger.classList.remove('active');
        navMenu.classList.remove('active');
    });
});

// ===== NAV LINK ATIVO AO SCROLL =====
const sections = document.querySelectorAll('section[id]');
const navLinks = document.querySelectorAll('.nav-link');

window.addEventListener('scroll', () => {
    let current = '';
    sections.forEach(section => {
        const sectionTop = section.offsetTop - 120;
        if (window.scrollY >= sectionTop) {
            current = section.getAttribute('id');
        }
    });
    navLinks.forEach(link => {
        link.classList.remove('active');
        if (link.getAttribute('href') === '#' + current) {
            link.classList.add('active');
        }
    });
});

// ===== ANIMAÇÃO DE ENTRADA (INTERSECTION OBSERVER) =====
const observerOptions = {
    threshold: 0.15,
    rootMargin: '0px 0px -60px 0px'
};

const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            entry.target.style.opacity = '1';
            entry.target.style.transform = 'translateY(0)';
            observer.unobserve(entry.target);
        }
    });
}, observerOptions);

// Aplicar animação aos cards
document.querySelectorAll('.historia-card, .servico-card, .contato-item, .cartao-visita, .contato-form').forEach((el, i) => {
    el.style.opacity = '0';
    el.style.transform = 'translateY(40px)';
    el.style.transition = `opacity 0.7s ease ${i * 0.08}s, transform 0.7s ease ${i * 0.08}s`;
    observer.observe(el);
});

// ===== FORMULÁRIO DE CONTATO =====
const contatoForm = document.getElementById('contatoForm');
contatoForm.addEventListener('submit', (e) => {
    e.preventDefault();
    const nome = document.getElementById('nome').value;
    const email = document.getElementById('email').value;
    const assunto = document.getElementById('assunto').value;
    const mensagem = document.getElementById('mensagem').value;

    // Validação simples
    if (!nome || !email || !mensagem) {
        mostrarNotificacao('Por favor, preencha todos os campos obrigatórios.', 'erro');
        return;
    }

    // Simulação de envio (aqui você conectaria com um backend/API)
    mostrarNotificacao(`Obrigado, ${nome}! Sua mensagem foi enviada com sucesso. Responderei em breve. 🐾`, 'sucesso');
    contatoForm.reset();
});

// ===== NOTIFICAÇÃO =====
function mostrarNotificacao(mensagem, tipo) {
    const notif = document.createElement('div');
    notif.className = `notificacao ${tipo}`;
    notif.textContent = mensagem;
    notif.style.cssText = `
        position: fixed;
        top: 100px;
        right: 24px;
        background: ${tipo === 'sucesso' ? 'linear-gradient(135deg, #0a6ebd, #1abc9c)' : '#e74c3c'};
        color: #fff;
        padding: 16px 26px;
        border-radius: 14px;
        box-shadow: 0 12px 32px rgba(0,0,0,0.2);
        font-family: 'Poppins', sans-serif;
        font-size: 0.9rem;
        font-weight: 500;
        z-index: 9999;
        max-width: 340px;
        transform: translateX(400px);
        transition: transform 0.5s cubic-bezier(0.68, -0.55, 0.27, 1.55);
    `;
    document.body.appendChild(notif);
    setTimeout(() => { notif.style.transform = 'translateX(0)'; }, 100);
    setTimeout(() => {
        notif.style.transform = 'translateX(400px)';
        setTimeout(() => notif.remove(), 500);
    }, 4000);
}

// ===== BAIXAR CARTÃO (geração de imagem via Canvas) =====
function baixarCartao() {
    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');
    const w = 1050, h = 600;
    canvas.width = w;
    canvas.height = h;

    // Fundo gradiente
    const grad = ctx.createLinearGradient(0, 0, w, h);
    grad.addColorStop(0, '#0a6ebd');
    grad.addColorStop(1, '#1abc9c');
    ctx.fillStyle = grad;
    roundRect(ctx, 0, 0, w, h, 40);
    ctx.fill();

    // Círculos decorativos
    ctx.fillStyle = 'rgba(255,255,255,0.08)';
    ctx.beginPath();
    ctx.arc(920, 80, 200, 0, Math.PI * 2);
    ctx.fill();
    ctx.beginPath();
    ctx.arc(120, 520, 150, 0, Math.PI * 2);
    ctx.fill();

    // Nome
    ctx.fillStyle = '#ffffff';
    ctx.font = 'bold 52px Georgia, serif';
    ctx.fillText('Dr. Davi Luiz', 70, 200);
    ctx.fillText('Sousa Pereira', 70, 265);

    // Cargo
    ctx.font = '600 20px Arial';
    ctx.fillStyle = 'rgba(255,255,255,0.9)';
    ctx.fillText('MÉDICO VETERINÁRIO', 72, 310);

    // Divisor
    ctx.fillStyle = 'rgba(255,255,255,0.5)';
    ctx.fillRect(70, 340, 90, 4);

    // Contatos
    ctx.font = '18px Arial';
    ctx.fillStyle = 'rgba(255,255,255,0.95)';
    ctx.fillText('📞  (00) 00000-0000', 70, 395);
    ctx.fillText('✉️  davi.vet@email.com', 70, 435);
    ctx.fillText('📍  Atendimento em domicílio e clínica', 70, 475);

    // Slogan
    ctx.font = 'italic 17px Arial';
    ctx.fillStyle = 'rgba(255,255,255,0.85)';
    ctx.fillText('🐾 Cuidando com amor e ciência', 70, 545);

    // Cruz veterinária decorativa
    ctx.fillStyle = 'rgba(255,255,255,0.9)';
    ctx.fillRect(920, 420, 20, 70);
    ctx.fillRect(895, 445, 70, 20);

    // Download
    const link = document.createElement('a');
    link.download = 'cartao-davi-luiz-veterinario.png';
    link.href = canvas.toDataURL('image/png');
    link.click();
    mostrarNotificacao('Cartão baixado com sucesso! 📥', 'sucesso');
}

function roundRect(ctx, x, y, w, h, r) {
    ctx.beginPath();
    ctx.moveTo(x + r, y);
    ctx.arcTo(x + w, y, x + w, y + h, r);
    ctx.arcTo(x + w, y + h, x, y + h, r);
    ctx.arcTo(x, y + h, x, y, r);
    ctx.arcTo(x, y, x + w, y, r);
    ctx.closePath();
}

// ===== IMPRIMIR CARTÃO =====
function imprimirCartao() {
    const conteudo = document.getElementById('cartaoVisita').innerHTML;
    const win = window.open('', '', 'width=900,height=650');
    win.document.write(`
        <html>
        <head>
            <title>Cartão de Visita - Dr. Davi Luiz</title>
            <style>
                body { display: flex; justify-content: center; align-items: center; min-height: 100vh; background: #f0f9ff; font-family: Arial, sans-serif; margin: 0; }
                .cartao-front {
                    width: 420px; height: 250px;
                    background: linear-gradient(135deg, #0a6ebd, #1abc9c);
                    border-radius: 20px; padding: 28px; position: relative;
                    overflow: hidden; box-shadow: 0 20px 50px rgba(10,110,189,0.35);
                    color: #fff; box-sizing: border-box;
                }
                .cartao-logo { width: 54px; height: 54px; }
                .cartao-logo svg { width: 100%; height: 100%; }
                .cartao-info h3 { font-family: Georgia, serif; font-size: 1.5rem; margin: 10px 0 4px; }
                .cartao-info .cargo { font-size: 0.85rem; text-transform: uppercase; letter-spacing: 2px; opacity: 0.9; }
                .cartao-divisor { width: 50px; height: 3px; background: rgba(255,255,255,0.6); border-radius: 3px; margin: 12px 0; }
                .cartao-contatos p { font-size: 0.82rem; opacity: 0.95; line-height: 1.7; margin: 4px 0; }
                .cartao-slogan { position: absolute; bottom: 18px; right: 24px; font-size: 0.75rem; opacity: 0.85; font-style: italic; }
                .cartao-bg-shapes .cb-shape { position: absolute; border-radius: 50%; background: rgba(255,255,255,0.1); }
                .cb-shape-1 { width: 180px; height: 180px; top: -70px; right: -50px; }
                .cb-shape-2 { width: 120px; height: 120px; bottom: -50px; left: -30px; }
            </style>
        </head>
        <body>
            <div class="cartao-front">${conteudo}</div>
            <script>window.onload = () => window.print();<\/script>
        </body>
        </html>
    `);
    win.document.close();
}

// ===== EFEITO PARALLAX SUAVE NO HERO =====
window.addEventListener('scroll', () => {
    const scrolled = window.scrollY;
    const shapes = document.querySelectorAll('.hero-bg-shapes .shape');
    shapes.forEach((shape, i) => {
        shape.style.transform = `translateY(${scrolled * (0.1 + i * 0.05)}px)`;
    });
});

// ===== EFEITO 3D NO CARTÃO AO MOVER O MOUSE =====
const cartaoVisita = document.getElementById('cartaoVisita');
if (cartaoVisita) {
    cartaoVisita.addEventListener('mousemove', (e) => {
        const rect = cartaoVisita.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;
        const centerX = rect.width / 2;
        const centerY = rect.height / 2;
        const rotateX = ((y - centerY) / centerY) * -10;
        const rotateY = ((x - centerX) / centerX) * 10;
        const front = cartaoVisita.querySelector('.cartao-front');
        front.style.transform = `rotateX(${rotateX}deg) rotateY(${rotateY}deg) translateY(-8px)`;
    });

    cartaoVisita.addEventListener('mouseleave', () => {
        const front = cartaoVisita.querySelector('.cartao-front');
        front.style.transform = 'rotateY(0) rotateX(0) translateY(0)';
    });
}

// ===== SMOOTH SCROLL COM OFFSET =====
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
    anchor.addEventListener('click', function (e) {
        e.preventDefault();
        const target = document.querySelector(this.getAttribute('href'));
        if (target) {
            const offset = 90;
            const targetPos = target.getBoundingClientRect().top + window.scrollY - offset;
            window.scrollTo({ top: targetPos, behavior: 'smooth' });
        }
    });
});

console.log('🐾 Site do Dr. Davi Luiz Sousa Pereira carregado com sucesso!');
