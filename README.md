# Raio-X da Operação — Master Support

Página de portfólio e diagnóstico de maturidade operacional da Master Support.
Feita para ser acessada por QR code em eventos: o visitante conhece o portfólio,
responde seis perguntas, recebe um índice de 0 a 100, uma estimativa do custo da
indisponibilidade no ambiente dele e é roteado para o próximo passo adequado ao
que respondeu.

Primeira campanha: **BMC Helix Roadshow · São Paulo**. O evento é um parâmetro
trocável — a página serve para qualquer ação.

---

## Como publicar

É um site estático. Não tem build, não tem dependência de servidor.

### GitHub Pages

1. Suba estes arquivos na raiz do repositório (ou em `/docs`).
2. **Settings → Pages → Source:** `Deploy from a branch`, escolha a branch e a
   pasta correspondente.
3. A página fica em `https://<organização>.github.io/<repositório>/`.

O arquivo `.nojekyll` já está incluído: sem ele o GitHub Pages ignora pastas que
começam com underscore e pode interferir no processamento.

### Domínio próprio

Para servir em `diagnostico.mastersupport.com.br` ou similar, crie um arquivo
`CNAME` na raiz com o domínio, e aponte um registro DNS `CNAME` para
`<organização>.github.io`.

### Qualquer outra hospedagem

Copiar a pasta inteira para qualquer servidor de arquivos estáticos funciona —
Netlify, Vercel, S3, Nginx, IIS. Os caminhos são todos relativos.

---

## Configuração obrigatória antes de ir ao ar

No `index.html`, procure por `var CFG = {` (perto do início do script principal).
Enquanto esses campos estiverem vazios, os recursos correspondentes ficam
desligados.

```js
var CFG = {
  CAMPANHA: "BMC Helix Roadshow · São Paulo",
  WHATSAPP: "",
  SITE_CONTATO: "https://www.mastersupport.com.br/#contato",
  EMAIL: "comercial@mastersupport.com.br",
  AGENDA_MONITORAMENTO: "",
  AGENDA_ESPECIALISTA: "",
  AGENDA_ASSESSMENT: "",
  AGENDA_COMERCIAL: "",
  HUBSPOT_PORTAL_ID: "49225425",
  HUBSPOT_FORM_GUID: ""
};
```

| Campo | O que acontece se ficar vazio |
|---|---|
| `CAMPANHA` | A tarja de evento no topo some. Troque a cada ação. |
| `WHATSAPP` | O botão de WhatsApp não aparece. Formato: `5591999999999`. |
| `AGENDA_*` | O botão "Escolher um horário na agenda" não aparece naquela rota. Aceita link de Calendly, HubSpot Meetings ou equivalente. |
| **`HUBSPOT_FORM_GUID`** | **Os leads não chegam ao CRM.** Ficam apenas no `localStorage` do navegador do visitante e se perdem. |

### O GUID do HubSpot

É o item mais importante. Sem ele nada do que o visitante preenche sai do
aparelho dele.

Para obter: HubSpot → Marketing → Formulários → crie ou abra um formulário →
o GUID está na URL do editor, ou em *Compartilhar → Incorporar*, no campo
`formId`.

O formulário precisa aceitar estes campos:

`nome`, `email`, `empresa`, `cargo`, `telefone`, `origem`, `origem_detalhe`,
`origem_completa`, `campanha`, `sessao`, `status`, `etapa_alcancada`,
`total_etapas`, `percentual_concluido`, `desafio`, `resolvedores`, `usuarios`,
`deteccao`, `paradas`, `mttr`, `indice`, `faixa`, `rota`,
`horas_indisponiveis_ano`, `custo_estimado_ano`.

---

## O que a página faz

**Portfólio** — serviços, parcerias auditadas (BMC Premier Partner, Zabbix
Premium Delivery Partner, Oracle Exadata), o selo BMC Partner of the Year 2026
em tamanho cheio ao clicar, ferramentas operadas em produção, setores atendidos
e números da operação.

**Diagnóstico** — identificação do lead com validação de domínio corporativo
(e-mails pessoais são recusados), seis perguntas com leitura de especialista a
cada resposta, e uma tela de cálculo antes do resultado.

**Resultado** — índice de 0 a 100 dividido em visibilidade, resiliência e
medição, com um limitador: o índice não ultrapassa a faixa que o elo mais fraco
permite. Estimativa de custo da indisponibilidade com memória de cálculo aberta
e todas as premissas ajustáveis pelo visitante.

**Roteamento** — quatro destinos, escolhidos pelo que foi respondido: avaliação
de monitoramento, sessão técnica com especialista, assessment de ITSM ou
conversa com o time comercial.

**PDF** — o visitante baixa o diagnóstico em um documento A4 de duas páginas
gerado no navegador, na identidade da marca.

**Rastreio de abandono** — quem desiste no meio também é registrado, com os
dados de contato e as respostas até ali, para follow-up por e-mail.

---

## Estrutura

```
index.html                 a página inteira: marcação, estilo e script
vendor/jspdf.umd.min.js    gerador de PDF, carregado só quando alguém pede o PDF
assets/                    logos, marca, selo e logotipos de parceiros
.nojekyll                  desliga o processamento Jekyll do GitHub Pages
```

A fonte Inter está embutida no `index.html` como subconjunto woff2, então a
página não depende de CDN nem do Google Fonts e renderiza igual offline.

O `vendor/jspdf.umd.min.js` tem 410 KB e **não** entra na primeira carga: só é
buscado quando o visitante clica em baixar o PDF. Isso mantém a abertura da
página leve no celular, que é como ela será usada no evento.

---

## Manutenção

**Trocar o evento:** altere `CFG.CAMPANHA`.

**Trocar as perguntas:** o array `Q` define as seis perguntas, suas opções, os
pesos de cada resposta e a leitura do especialista que aparece após a escolha.

**Trocar o roteamento:** o objeto `ROUTES` define os quatro destinos, e a função
`route()` decide qual sai a partir das respostas.

**Trocar o cálculo de custo:** a função `cost()` e o objeto `costState` com os
valores iniciais das premissas.

**Trocar parceiros e ferramentas:** os arrays `PARTNERS` e `TOOLS`.

---

Material interno da Master Support. Contém marcas de terceiros (BMC, Zabbix,
Oracle, Prometheus) usadas com base nas parcerias vigentes.
