# AGENTS.md

## Projeto

Website oficial do projeto "Caça ao Tesouro".

A identidade visual do projeto é inspirada em:

- exploração
- aventura
- mapas antigos
- bússolas
- caça ao tesouro
- descoberta
- navegação marítima
- estilo premium temático

Toda interface deve transmitir sensação de:

- mistério
- aventura
- descoberta
- curiosidade
- imersão

Evitar aparência:

- corporativa
- SaaS genérico
- excessivamente minimalista
- tecnológica fria
- neon
- Material Design puro

---

# Stack Tecnológica

Frontend:

- Next.js
- React
- TypeScript
- TailwindCSS
- shadcn/ui

Preferências:

- TypeScript strict mode
- Componentes reutilizáveis
- Server Components quando possível
- Responsividade mobile-first
- SEO otimizado
- Performance otimizada

---

# Identidade Visual

## Logo Oficial

Arquivos da logomarca:

- Logo com maior definição de imagem e tamanho
  `/docs/branding/Logo_1284x627px.png`

- Logo com menor definição de imagem e tamanho
  `/docs/branding/Logo_148x72px.png`

A logo possui:

- textura de madeira
- letras douradas
- bússola temática
- estilo aventura/exploração

A interface deve respeitar integralmente esta identidade visual.

---

# Paleta de Cores Oficial

```txt
colors: {
  primary: '#7D420C',
  primaryDark: '#4E2A08',

  accent: '#F7B400',
  accentSoft: '#D89B00',

  background: '#F5E6C8',
  surface: '#FFF8ED',

  text: '#1A1A1A',
  border: '#D6C3A5',
}
```

# Diretrizes Visuais

## Estilo Geral

O site deve ter:

- aparência cinematográfica
- sensação de aventura
- estilo premium
- textura leve inspirada em mapas antigos
- profundidade visual suave
- sombras elegantes
- elementos temáticos discretos

## Elementos Visuais Permitidos

Pode utilizar:

- bússolas
- mapas
- pergaminhos
- moedas
- rotas pontilhadas
- madeira
- ícones de exploração
- textura de papel antigo
- elementos náuticos

## Elementos Visuais Proibidos

Evitar:

- visual corporativo moderno padrão
- excesso de branco puro
- gradientes neon
- efeitos futuristas
- glassmorphism exagerado
- excesso de animações
- visual gamer
- UI extremamente flat

## Tipografia

Preferência:

- títulos com aparência temática elegante
- textos com excelente legibilidade

Sugestões:

- Cinzel
- Merriweather
- Inter
- Playfair Display

Combinação recomendada:

- títulos temáticos
- conteúdo moderno e limpo
- Botões

Botões principais:

- fundo Accent (#F7B400)
- texto escuro
- hover com Accent Soft (#D89B00)

Botões secundários:

- fundo Primary (#7D420C)
- texto claro

Todos os botões devem possuir:

- bordas suaves
- sombras discretas
- hover elegante
- boa acessibilidade
- Cards

Os cards devem:

- utilizar fundo Surface (#FFF8ED)
- possuir bordas suaves
- ter leve profundidade
- transmitir aspecto premium

Evitar:

- cards extremamente flat
- excesso de sombra
- transparências exageradas

## Layout

O layout deve:

- ser responsivo
- mobile-first
- possuir boa hierarquia visual
- utilizar espaçamento confortável
- priorizar experiência visual imersiva

### Hero Section

A Hero Section deve:

- causar impacto visual
- utilizar elementos temáticos
- possuir CTAs destacados
- transmitir imediatamente o conceito de aventura

Gradiente recomendado:

```txt
background: linear-gradient(
  135deg,
  #7D420C,
  #4E2A08
);
```

### Animações

As animações devem ser:

- suaves
- elegantes
- discretas

Pode utilizar:

- fade
- hover suave
- parallax leve
- transições lentas

Evitar:

- animações rápidas
- efeitos chamativos
- excesso de movimento
- UX/UI

A experiência deve transmitir:

- descoberta
- exploração
- curiosidade
- diversão
- navegação intuitiva

A navegação deve ser:

- clara
- simples
- imersiva
- agradável

### Estrutura Frontend

```txt
/src
  /app
  /components
  /hooks
  /services
  /lib
  /styles
```

Convenções de Código:

- Utilizar TypeScript fortemente tipado
- Não utilizar any
- Componentes pequenos e reutilizáveis
- Separar lógica de UI
- Evitar duplicação
- Priorizar legibilidade
- Acessibilidade

Sempre garantir:

- contraste adequado
- navegação por teclado
- textos legíveis
- botões acessíveis
- hover e focus states

### SEO

Implementar:

- metadata adequada
- OpenGraph
- títulos semânticos
- imagens otimizadas
- performance elevada

### Performance

Priorizar:

- lazy loading
- otimização de imagens
- componentes leves
- carregamento rápido

Regras Importantes:

- Nunca descaracterizar a identidade visual
- Sempre manter coerência temática
- Todas as páginas devem seguir a mesma linguagem visual
- Não criar componentes fora da paleta definida
- Não utilizar cores aleatórias

### Referências Visuais:

Inspirar-se em:

- Indiana Jones
- mapas antigos
- exploração marítima
- treasure hunt
- bússolas clássicas
- pergaminhos premium

Modernizar a experiência mantendo o tema aventura.
