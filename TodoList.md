### 🥃 

### 🥃 全职或兼职, 远程工作, 软件工程师, 亚太地区

### 🥃 Deepfake

### 🥃 头像生成，声音处理，生成视频

用处理过的头像，处理过的声音，生成视频

### 🥃 Expo Go: iOS, Android 应用

### 🥃 声音转图像的mobile应用

### 🥃 大量markdown文本管理

### 🥃 resume自动匹配

### 🥃 logo, favicon, relative video, avatar生成

- Logo → SVG
- Favicon → ICO (multi-size), PNG (fallbacks)
- `npx favicons logo.svg --output ./public # create .icon + png favicons`



### 🥃 email, cloud resources 管理 

### 🥃 

### 🥃 

### 🥃 ffmpeg

### 🥃 convert Figma design to code with AI using Google Vertex-ai ?

### 🥃 Toolchain

### 🥃 

### 🥃 Student Verification

- Google AI Pro (https://one.google.com/ai?utm_source=gemini&utm_medium=web&utm_campaign=zero_state_banner&g1_landing_page=75)
- GitHub Copilot


## 2025-10-14

npx create-next-app@latest my-app --typescript --tailwind --eslint --app
cd my-app

npx shadcn@latest init
npx shadcn@latest add button

### 1. General UI Theme overhaul Prompt

Refactor my Next.js app using Tailwind CSS v4 and shadcn/ui to apply a modern, professional UI theme. Use a primary color of #3B82F6 (blue-500) for accents, secondary #10B981 (green-500) for success states, and neutral grays like #1F2937 (gray-800) for backgrounds in dark mode. Enable dark mode with CSS variables in globals.css, toggling via class="dark". Set base font to Inter (via next/font/google) with sizes: base 16px, headings h1 2.5rem (bold, tracking-tight), h2 1.875rem, paragraphs 1rem (leading-relaxed). Add subtle shadows (shadow-md) and rounded corners (rounded-lg) to cards and modals. For layouts, use flex or grid with responsive breakpoints: mobile-first, md: for tablets, lg: for desktops. Ensure accessibility with focus-visible rings in primary color. Update tailwind.config.ts to extend theme with these colors and add animations via tailwindcss-animate. Provide code snippets for layout.tsx, globals.css, and example components like Button and Card from shadcn/ui, overriding defaults with variants for hover (scale-105 transition) and disabled states (opacity-50 cursor-not-allowed).

### 2. Layout and responsive design prompt

Enhance the layout in my Next.js app with Tailwind CSS and shadcn/ui for a clean, responsive dashboard-style UI. Use CSS Grid for main sections: grid-cols-1 on mobile, grid-cols-2 md:grid-cols-3 lg:grid-cols-4 on larger screens, with gap-6. Set max-width container mx-auto px-4 sm:px-6 lg:px-8 for centering. Add sticky headers (sticky top-0 z-50) with bg-background/95 backdrop-blur supports-blur:blur for glassmorphism effect. For sidebars, use fixed or absolute positioning with w-64 hidden md:block, transitioning on hover. Incorporate shadcn/ui components like Sidebar, Navbar, and Tabs with custom styles: active tabs with border-b-2 border-primary, hover bg-muted/50. Make everything mobile-friendly with touch-friendly padding (p-4) and larger tap targets (min-h-12). Provide updated code for app/layout.tsx and a sample page.tsx with a hero section (flex flex-col items-center justify-center min-h-screen bg-gradient-to-br from-background to-muted).

### 3. Color Scheme and Theming Prompt

Evolve the color palette in my Tailwind + shadcn/ui setup for a vibrant, modern aesthetic. Define CSS variables in globals.css: --primary: 221 83% 53% (blue-500 hsl), --secondary: 142 76% 36% (emerald-600), --accent: 346 100% 50% (pink-500 for highlights), --background: 0 0% 100% (white light) / 240 10% 3.9% (dark). For dark mode, invert contrasts with --foreground: 0 0% 98%. Apply to shadcn components: Buttons with bg-primary text-primary-foreground hover:bg-primary/90, Cards with border border-border bg-card shadow-sm hover:shadow-md. Add theme toggle using shadcn's ThemeProvider or a custom Switch component. Ensure color contrast ratios >4.5:1 for accessibility. Update tailwind.config.ts to include these in theme.extend.colors, and provide examples for overriding Button, Input, and Select components with the new palette.

### 4. Typography and Font Customization Prompt

Improve typography in my Next.js UI with Tailwind and shadcn/ui for a sleek, readable design. Import fonts: primary Inter (variable font for weights 100-900), fallback sans-serif; secondary Mono for code blocks (e.g., font-mono). Set global styles: html font-size 16px, body font-family var(--font-inter), line-height 1.5, text-base antialiased. Headings: h1 font-size 2.25rem font-weight 800 tracking-tight, h2 1.5rem font-weight 700, h3 1.25rem. Paragraphs: text-base leading-7 text-muted-foreground. For buttons and labels: font-size 0.875rem font-medium uppercase tracking-wide. Add responsive scaling: sm:text-lg for larger screens. Customize shadcn components like Typography (if using) or default <p>, <h1> with utilities like prose prose-invert for dark mode. Provide code for fonts import in layout.tsx, globals.css updates, and examples for text-heavy components like Accordion or Tooltip with improved kerning and hyphenation.

### 5. Button and Interactive Elements Prompt

Redesign buttons and form elements in my Tailwind + shadcn/ui app for a modern, interactive feel. For Button: base rounded-md px-4 py-2 text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 ring-primary disabled:pointer-events-none disabled:opacity-50. Add variants: primary bg-primary text-primary-foreground hover:bg-primary/90 active:scale-95, outline border border-input bg-transparent hover:bg-accent/10. Include icons with flex items-center gap-2. For Inputs: rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background file:border-0 file:bg-transparent placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 ring-ring disabled:cursor-not-allowed disabled:opacity-50. Add error states with border-destructive. Use animations: hover:scale-105 transition-transform duration-200 ease-in-out. Ensure touch-friendly sizes (min-h-10). Provide refactored button.tsx, input.tsx from shadcn/ui, and usage in a sample form component with validation feedback.

### 6. Card and Container Components Prompt

Enhance card-like components in my Next.js UI with Tailwind and shadcn/ui for a neumorphic or glassmorphic modern style. For Card: bg-card text-card-foreground rounded-xl border border-border shadow-lg overflow-hidden transition-shadow hover:shadow-xl. Add header (p-6 border-b), content (p-6), footer (p-6 border-t flex justify-end). Apply glassmorphism: bg-background/30 backdrop-blur-md supports-blur:blur-md border-border/50. For modals/Dialog: overlay bg-background/80 backdrop-blur-sm, content rounded-2xl shadow-2xl p-8 max-w-md mx-auto. Use grid or flex inside for layout. Responsive: full-width on mobile with md:max-w-lg. Customize shadcn Card, Dialog, and Sheet with these styles in their .tsx files. Provide examples for a product card with image (aspect-video object-cover), title (font-semibold text-lg), and actions (flex gap-2).

### 7. Advanced Effects and Animations Prompt

Add modern animations and effects to my Tailwind CSS + shadcn/ui components for engaging UI evolution. Install tailwindcss-animate if needed. For hover: transition-all duration-300 ease-in-out hover:scale-105 hover:rotate-1. Loading spinners: animate-spin with shadcn's Skeleton (pulse bg-muted/50). Fade-ins: opacity-0 animate-fade-in. For toasts/Alert: slide-in from bottom with animate-slide-up. Update tailwind.config.ts with custom animations: keyframes { fade-in: { '0%': { opacity: 0 }, '100%': { opacity: 1 } } }, animation: { fade-in: 'fade-in 0.5s ease-out' }. Apply to buttons (hover:bg-gradient-to-r from-primary to-accent), cards (hover:border-primary), and navigation (active:underline underline-offset-4). Ensure performance: no heavy animations on mobile. Provide code snippets for animated components like HoverCard, Popover, and a loading page with Progress bar.
