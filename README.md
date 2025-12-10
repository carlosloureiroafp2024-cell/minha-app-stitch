<!DOCTYPE html>
<html lang="pt">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>ATTRA - Wellness App Prototype</title>
  
  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/history@5/umd/history.development.js"></script>
  <script src="https://unpkg.com/react-router@6.3.0/umd/react-router.development.js"></script>
  <script src="https://unpkg.com/react-router-dom@6.3.0/umd/react-router-dom.development.js"></script>
  <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
  
  <script src="https://cdn.tailwindcss.com?plugins=forms,container-queries"></script>
  <link href="https://fonts.googleapis.com/css2?family=Manrope:wght@400;500;600;700;800&display=swap" rel="stylesheet" />
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:ital,wght@0,200..800;1,200..800&display=swap" rel="stylesheet" />
  <link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined" rel="stylesheet" />

  <script>
    tailwind.config = {
      darkMode: "class",
      theme: {
        extend: {
          colors: {
            // Combined colors from all screens
            "primary": "#4A55A2",
            "background-light": "#f6f6f8",
            "background-dark": "#101022",
            "accent-lavender": "#E6E6FA",
            "accent-gold": "#D4AF37",
            "indigo-base": "#363C81",
            "gold-soft": "#D4AF37",
            "lavender-light": "#E6E6FA",
            "surface-dark": "#232348",
            "border-dark": "#323267",
            "card-dark": "#1E1A2B",
            "text-light": "#E6E6FA",
            "text-dark": "#f0f0f8",
            "text-muted-light": "#4c4c9a",
            "text-muted-dark": "#a0a0c0",
          },
          fontFamily: {
            "display": ["Manrope", "sans-serif"],
            "display-plus": ["Plus Jakarta Sans", "sans-serif"],
          },
          borderRadius: {
            "DEFAULT": "1rem",
            "lg": "1.5rem",
            "xl": "2rem",
            "full": "9999px"
          },
        },
      },
    }
  </script>
  <style>
    body {
      min-height: max(884px, 100dvh);
      font-family: 'Manrope', sans-serif;
    }
    .font-display-plus {
        font-family: 'Plus Jakarta Sans', sans-serif;
    }
    .material-symbols-outlined {
      font-variation-settings: 'FILL' 0, 'wght' 400, 'GRAD' 0, 'opsz' 24;
    }
    .material-symbols-outlined.fill, .material-symbols-outlined.icon-filled {
      font-variation-settings: 'FILL' 1, 'wght' 400, 'GRAD' 0, 'opsz' 24;
    }
    .segmented-control input { display: none; }
    .segmented-control input:checked + label { background-color: #303F9F; color: white; }
    .dark .segmented-control input:checked + label { background-color: #E6E6FA; color: #101022; }
  </style>
<style>
    body {
      min-height: max(884px, 100dvh);
    }
  </style>
  </head>
<body class="bg-background-light dark:bg-background-dark">
  <div id="root"></div>

  <script type="text/babel">
    const { useState, useEffect } = React;
    const { createRoot } = ReactDOM;
    const { MemoryRouter, Routes, Route, Link, useNavigate } = ReactRouterDOM;

    // --- SHARED COMPONENTS ---

    const Icon = ({ name, className = '', filled = false }) => (
      <span className={`material-symbols-outlined ${filled ? 'fill' : ''} ${className}`}>
        {name}
      </span>
    );

    const NavItem = ({ to, icon, label, activeIcon }) => {
        const isCurrentPage = window.location.hash === `#${to}`;
        const currentIcon = isCurrentPage ? activeIcon || icon : icon;

        // Base styles
        let linkClasses = "flex flex-1 flex-col items-center justify-end gap-1 text-zinc-500 dark:text-zinc-400";
        let textClasses = "text-xs font-medium leading-normal tracking-[0.015em]";
        let iconFilled = isCurrentPage;

        // Active styles
        if (isCurrentPage) {
            linkClasses = "flex flex-1 flex-col items-center justify-end gap-1 text-primary";
            textClasses = "text-xs font-bold leading-normal tracking-[0.015em] text-primary";
        }
        
        return (
            <Link to={to} className={linkClasses}>
                <div className="flex h-8 items-center justify-center">
                    <Icon name={currentIcon} filled={iconFilled} />
                </div>
                <p className={textClasses}>{label}</p>
            </Link>
        );
    };
    
    const BottomNav = () => {
        return (
            <nav className="fixed bottom-0 left-0 right-0 z-10 mx-auto max-w-md border-t border-zinc-200/50 dark:border-zinc-800 bg-background-light/80 dark:bg-background-dark/80 backdrop-blur-sm">
                <div className="flex justify-around p-2">
                    <NavItem to="/home" icon="home" label="Início" />
                    <NavItem to="/meditar" icon="self_improvement" label="Meditar" />
                    <NavItem to="/diario" icon="edit_note" label="Diário" />
                    <NavItem to="/progresso" icon="bar_chart" activeIcon="query_stats" label="Progresso" />
                    <NavItem to="/perfil" icon="person" label="Perfil" />
                </div>
            </nav>
        );
    };

    // --- SCREEN COMPONENTS ---

    const WelcomePage = () => {
        const navigate = useNavigate();
        const handleStart = () => navigate('/home');

        return (
            <div className="relative flex h-screen w-full flex-col bg-gradient-to-b from-[#3a3f72] to-[#1e224b] font-display-plus">
                <div className="absolute inset-0 z-0 opacity-20">
                    <div className="absolute inset-0 bg-center bg-no-repeat bg-cover" style={{backgroundImage: `url("https://lh3.googleusercontent.com/aida-public/AB6AXuC0VSCQXb0EBySYBKmTUfi8PHTsbPTJDA-RXUrwECwXWeqfeKcKxcX5mnoE_4vF9oyt0f_fhIOVCeNCWHbiQkqQrBql7oSw8aVcw-_7QNUsjLcp0_5eg-W9usx8VeNiIiqhRCY9soGgx6IPZ8AMD2Zbny3dPCR4OPB6q3V2ls8qY5-OKZuBzsIUBMkiIQQI_4CZNBuHI9FNA6pquc2Y4QsT7bFxP7aQiXBrahZpEHfasWWqAJg4VN21wbNGWydU0vf1r9l4VJVjiHg")`}}></div>
                </div>
                <div className="relative z-10 flex h-full min-h-screen w-full flex-col justify-between px-4 pb-8 pt-16">
                    <div className="flex flex-col items-center">
                        <img alt="ATTRA logo" className="h-24 w-24" src="https://lh3.googleusercontent.com/aida-public/AB6AXuAuDDEbieOE6MxH7yIrOGowneDcjN_qgstgX55kx0prXjL_U-yhjkkgRU9-HTMxMPG_6BKlra4oYsHrY1KCZVKNzy7MI9Ia1wBGHOLQN2cu_YL5o6_x-6eY28IxVRs5eVOK9_0EEjOcmthuWXvpqtikBQTUjhiydXUm_8-KdLz94pDRdumLSYUt7HoBfNOym_MU9QdzSOabsloDvsgoSocnThGkco8UoNh7xvpl9xjo3dy8tLBDzHfgxJBtJf2u71OYQUoRRn3Xrkg" />
                        <h1 className="text-lavender-light text-[22px] font-bold leading-tight tracking-[-0.015em] text-center pt-8">Bem-vindo ao Teu Caminho Interior</h1>
                        <p className="text-lavender-light/80 text-base font-normal leading-normal text-center pt-2 max-w-sm">Descobre o bem-estar, meditação e desenvolvimento pessoal para uma vida plena.</p>
                    </div>
                    <div className="flex flex-1 gap-4 max-w-[480px] flex-col items-stretch w-full mx-auto mt-12 justify-end">
                        <button onClick={handleStart} className="flex min-w-[84px] max-w-[480px] cursor-pointer items-center justify-center overflow-hidden rounded-xl h-14 px-5 bg-gradient-to-r from-indigo-base to-gold-soft text-white text-base font-bold leading-normal tracking-[0.015em] w-full transition-transform duration-200 hover:scale-105 active:scale-100 shadow-lg shadow-indigo-500/20">
                            <span className="truncate">Começar</span>
                        </button>
                        <button onClick={handleStart} className="flex min-w-[84px] max-w-[480px] cursor-pointer items-center justify-center overflow-hidden rounded-xl h-14 px-5 bg-gradient-to-r from-indigo-base to-gold-soft/80 text-white text-base font-bold leading-normal tracking-[0.015em] w-full transition-transform duration-200 hover:scale-105 active:scale-100 shadow-lg shadow-indigo-500/20">
                            <span className="truncate">Entrar</span>
                        </button>
                    </div>
                </div>
            </div>
        );
    };

    const HomePage = () => {
        return (
            <div className="relative mx-auto flex h-screen max-w-md flex-col overflow-hidden bg-background-light dark:bg-background-dark pb-20">
                <header className="flex items-center bg-background-light dark:bg-background-dark p-4 pb-2 justify-between sticky top-0 z-10">
                    <div className="flex size-12 shrink-0 items-center">
                        <div className="bg-center bg-no-repeat aspect-square bg-cover rounded-full size-10" style={{backgroundImage: `url("https://lh3.googleusercontent.com/aida-public/AB6AXuBmNkaIDd4zbWVyBRMXmfNMggj3IrVQPZOb7iP_Wx9SnLIVt8Rr-UGDO51q40ISnEDa3DzWwaVvYY-qrfMlhWeejnFW5LPkFqfonikol8Ry0Scey7wFZCVwRnRiWXqh8DabUfZXsjPf4IqSmjEcDrTUQHUvJo06LSGaOLL2VrukAVRyrBVaTj7zua77WYCkXsKUsdSlvzUDA2yHw5VwgOTLeE83YZZgmD61a0_f5WEZZ5O3KfvawEY_sRFiT2N5euONw3zbG40a1iw")`}}></div>
                    </div>
                    <h1 className="text-text-light dark:text-text-dark text-lg font-bold leading-tight tracking-[-0.015em] flex-1 text-center">O Meu Caminho</h1>
                    <div className="flex w-12 items-center justify-end">
                        <button className="flex max-w-[480px] cursor-pointer items-center justify-center overflow-hidden rounded-full h-12 bg-transparent text-text-light dark:text-text-dark gap-2 text-base font-bold leading-normal tracking-[0.015em] min-w-0 p-0">
                            <Icon name="notifications" className="text-text-light dark:text-text-dark" />
                        </button>
                    </div>
                </header>
                <main className="flex-1 overflow-y-auto">
                    <div className="p-4">
                        <div className="bg-gradient-to-br from-[#4B0082] to-[#4B0082]/70 flex flex-col items-stretch justify-end rounded-xl pt-16 shadow-lg">
                            <div className="flex w-full flex-col gap-4 p-5">
                                <div className="flex items-center justify-between">
                                    <p className="text-[#F5E8C7] text-sm font-medium leading-normal">Pensamento Quântico do Dia</p>
                                    <button className="text-[#F5E8C7]/80 hover:text-[#F5E8C7]">
                                        <Icon name="share" className="text-xl" />
                                    </button>
                                </div>
                                <p className="text-white tracking-light text-2xl font-bold leading-tight max-w-[440px]">A sua realidade é um reflexo dos seus pensamentos mais profundos.</p>
                            </div>
                        </div>
                    </div>
                    <section className="flex flex-col gap-3 p-4">
                        <div className="flex items-center justify-between">
                            <h2 className="text-text-light dark:text-text-dark text-xl font-bold leading-tight tracking-[-0.015em]">Recomendações AI Personalizadas</h2>
                            <button className="text-text-muted-light dark:text-text-muted-dark text-sm font-semibold">Ver tudo</button>
                        </div>
                        <div className="flex gap-4 overflow-x-auto pb-2 -mx-4 px-4 snap-x snap-mandatory">
                            {/* Recommendation Cards */}
                            <div className="flex flex-col gap-3 rounded-xl bg-gradient-to-br from-[#E6E6FA] via-[#E6E6FA] to-[#F5E8C7]/50 dark:from-[#4B0082]/30 dark:to-[#4B0082]/20 p-4 snap-start w-72 shrink-0">
                                <a className="flex-grow" href="#">
                                    <div className="flex items-center gap-3">
                                        <div className="flex items-center justify-center bg-[#F5E8C7]/70 dark:bg-[#D4AF37]/30 rounded-full size-10 text-[#4B0082] dark:text-[#F5E8C7]"><Icon name="self_improvement" /></div>
                                        <p className="text-xs font-bold uppercase tracking-wider text-[#4B0082]/80 dark:text-[#F5E8C7]">MEDITAÇÃO</p>
                                    </div>
                                    <p className="mt-3 text-text-light dark:text-text-dark text-lg font-bold leading-snug">Meditação para Alívio da Ansiedade</p>
                                    <p className="mt-1 text-text-muted-light dark:text-text-muted-dark text-sm font-normal">Uma sessão guiada de 15 minutos para acalmar a sua mente e encontrar paz interior.</p>
                                </a>
                            </div>
                            <div className="flex flex-col gap-3 rounded-xl bg-gradient-to-br from-[#F5E8C7]/60 to-[#F5E8C7]/30 dark:from-[#D4AF37]/30 dark:to-[#D4AF37]/20 p-4 snap-start w-72 shrink-0">
                               <a className="flex-grow" href="#">
                                   <div className="flex items-center gap-3">
                                        <div className="flex items-center justify-center bg-[#E6E6FA] dark:bg-[#4B0082]/30 rounded-full size-10 text-[#D4AF37] dark:text-[#F5E8C7]"><Icon name="science" /></div>
                                        <p className="text-xs font-bold uppercase tracking-wider text-[#D4AF37] dark:text-[#F5E8C7]">FÍSICA QUÂNTICA</p>
                                   </div>
                                    <p className="mt-3 text-text-light dark:text-text-dark text-lg font-bold leading-snug">O Princípio da Incerteza na Sua Vida</p>
                                    <p className="mt-1 text-text-muted-light dark:text-text-muted-dark text-sm font-normal">Artigo da biblioteca sobre como a incerteza pode ser uma porta para infinitas possibilidades.</p>
                               </a>
                            </div>
                        </div>
                    </section>
                     <div className="grid grid-cols-[repeat(auto-fit,minmax(158px,1fr))] gap-4 p-4 pt-0">
                        {/* Grid Items */}
                        <a className="flex flex-col gap-3 pb-3" href="#">
                            <div className="w-full bg-center bg-no-repeat aspect-video bg-cover rounded-lg relative overflow-hidden" style={{backgroundImage: `url("https://lh3.googleusercontent.com/aida-public/AB6AXuAln1fXurI1wWYW9IU8fcPObhgGLGI3GLdJcdQ3BIu-fvOyu6i868f2xSOs6ncqPROHMOScBuzgX8oR8Q3T6LnEe8sC1m2WVqPBAEdHUZwuzos1LkKdDphTct5csmkayDFhGZwnQ4swL4nOd8lhLYyOb9Zau36DlVNrVwuRkAJIsYuoIdg_3MMp_GOKPJ55NRuuTKJENFjzaZqWG6bV_fbvSWqtmGSHIBiyMGod3wjY-ghX3V9_1JbR6p5jante7YIrkIks7hxDZmA")`}}>
                                <div className="absolute inset-0 bg-black/20 flex items-center justify-center"><Icon name="play_circle" className="text-white text-4xl" /></div>
                            </div>
                            <div>
                                <p className="text-text-light dark:text-text-dark text-base font-medium leading-normal">Meditação Diária</p>
                                <p className="text-text-muted-light dark:text-text-muted-dark text-sm font-normal leading-normal">10 min | Foco e Clareza</p>
                            </div>
                        </a>
                         <Link to="/diario" className="flex flex-col gap-3 pb-3">
                            <div className="w-full bg-center bg-no-repeat aspect-video bg-cover rounded-lg" style={{backgroundImage: `url("https://lh3.googleusercontent.com/aida-public/AB6AXuDU7VWhyy-QH2WfexpVrYiWMv2iuZnRs65p32knzsWvhNJmfVIPy1lRwf0C6HRAa1TJ6JUcksS_6lsO7bvxlyHDQc3HI_lij7ka_qUIV0Fsk_6BCjWF_I89TbyhbRdvVmt9QZAdH5u9f26JX2SI4L_UfD36F7hqBSDRpENqp10VRr_HY9bbzBBq4sbWT-p3VAWe6u4p4jX8T3oKw7rFW4wQYVBUpmcunjKipyV0Tdd7hR339vQPztXzerMvecqANX1DSkje5HVo5Qc")`}}></div>
                            <div>
                                <p className="text-text-light dark:text-text-dark text-base font-medium leading-normal">Diário de Gratidão</p>
                                <p className="text-text-muted-light dark:text-text-muted-dark text-sm font-normal leading-normal">Registe as suas manifestações</p>
                            </div>
                        </Link>
                    </div>
                </main>
                <BottomNav />
            </div>
        );
    };
    
    const HomePageV2 = () => {
         return (
            <div className="relative flex min-h-screen w-full flex-col bg-background-dark text-white max-w-md mx-auto">
                <header className="p-4 flex justify-between items-center">
                    <div className="flex flex-col">
                        <p className="text-white/70 text-base">Olá,</p>
                        <h1 className="text-white text-2xl font-bold">Joana</h1>
                    </div>
                    <button className="relative">
                        <Icon name="notifications" className="text-3xl text-white/80" />
                        <span className="absolute top-0 right-0 block h-2 w-2 rounded-full bg-gold-soft"></span>
                    </button>
                </header>
                <main className="flex-grow px-4 pb-28 space-y-6">
                    <section className="bg-card-dark p-5 rounded-xl">
                        <h2 className="text-sm font-semibold text-gold-soft uppercase tracking-wider mb-2">Pensamento Quântico do Dia</h2>
                        <p className="text-white/90 text-base italic leading-relaxed">"A realidade que experienciamos é um reflexo dos nossos pensamentos mais profundos. Mude os seus pensamentos, mude a sua realidade."</p>
                        <p className="text-right text-white/60 text-sm mt-3">- Dr. Joe Dispenza</p>
                    </section>
                    <section className="space-y-3">
                        <Link to="/meditar" className="flex items-center bg-card-dark p-4 rounded-xl gap-4">
                            <div className="flex items-center justify-center size-12 bg-primary/20 rounded-lg text-lavender-light"><Icon name="self_improvement" className="text-3xl"/></div>
                            <div>
                                <h3 className="font-bold text-white">Meditação Diária</h3>
                                <p className="text-sm text-white/60">Foco e Clareza - 10 min</p>
                            </div>
                            <Icon name="arrow_forward_ios" className="text-white/50 ml-auto" />
                        </Link>
                        <Link to="/diario" className="flex items-center bg-card-dark p-4 rounded-xl gap-4">
                            <div className="flex items-center justify-center size-12 bg-primary/20 rounded-lg text-lavender-light"><Icon name="edit_note" className="text-3xl" /></div>
                            <div>
                                <h3 className="font-bold text-white">Diário de Gratidão</h3>
                                <p className="text-sm text-white/60">Registe as suas manifestações</p>
                            </div>
                            <Icon name="arrow_forward_ios" className="text-white/50 ml-auto" />
                        </Link>
                         <Link to="/progresso" className="flex items-center bg-card-dark p-4 rounded-xl gap-4">
                            <div className="flex items-center justify-center size-12 bg-primary/20 rounded-lg text-lavender-light"><Icon name="checklist" className="text-3xl" /></div>
                            <div>
                                <h3 className="font-bold text-white">Plano de Ação Saudável</h3>
                                <p className="text-sm text-white/60">3 de 5 hábitos concluídos</p>
                            </div>
                            <Icon name="arrow_forward_ios" className="text-white/50 ml-auto" />
                        </Link>
                    </section>
                    <section>
                        <div className="flex justify-between items-center mb-3">
                            <h2 className="text-lg font-bold">Recomendações para si</h2>
                            <a className="text-sm font-semibold text-gold-soft" href="#">Ver todas</a>
                        </div>
                        <div className="grid grid-cols-2 gap-4">
                            <a className="space-y-2 group" href="#">
                                <div className="relative overflow-hidden rounded-xl">
                                    <img alt="Meditação para abundância" className="w-full h-28 object-cover group-hover:scale-105 transition-transform duration-300" src="https://lh3.googleusercontent.com/aida-public/AB6AXuB7d980mYj7GkQjBz-wpOmRBMrnok3oJx9PnpSEhytiXB16wvvuV97lFLDONyy_TDDpTvkI-ciTlRdWF3KMBeSl_n0E1UuT7uOKUJNCykS5CRUr95PJhKsximDQqVzF-gU7LaxVzqkTQywpptlWgwymChfYzQwcH3_WX9JpT-ei-rEBowdeopyW_E-eAP1OZkl9zr8O32hq7iEPfzoGqS0eDtbCun_56FZ6oAx4VXgMk7esoX9Q87bhjr_QHrz3mX_Z9jYDQ5HjqJg" />
                                    <div className="absolute top-2 right-2 bg-black/50 text-white text-xs px-2 py-1 rounded-full">12 min</div>
                                </div>
                                <div>
                                    <h4 className="font-bold text-white text-base leading-tight">Meditação da Abundância</h4>
                                    <p className="text-sm text-white/60">Atraia prosperidade</p>
                                </div>
                            </a>
                             <a className="space-y-2 group" href="#">
                                <div className="relative overflow-hidden rounded-xl">
                                    <img alt="Artigo sobre a Lei da Vibração" className="w-full h-28 object-cover group-hover:scale-105 transition-transform duration-300" src="https://lh3.googleusercontent.com/aida-public/AB6AXuA1Rk0Nf6LcfPTcUsJ_XifnkyHoCfoNgyufzf-Ry3m0qsHWetwzkKtJTIFAVZsqiuhmYeAYYlHkct3z_2pOaPmNJ5S30v7rL_kauTRBu3HGWPxlmQ6Q4Sg-tp_tBFHPAB60aeUUisNjNegBwRHWhFDjlWLeWkCkbppOEdumef2cqXSW0Iiwe2fUg3nEibCxbqdehmo2Bzi3IpAPR1BvFURYFY9qk2PEYix7naDPnqDEImhU-FR54HjcUFstDkAW9AJly8rnV9jeBks" />
                                    <div className="absolute top-2 right-2 bg-black/50 text-white text-xs px-2 py-1 rounded-full">Artigo</div>
                                </div>
                                <div>
                                    <h4 className="font-bold text-white text-base leading-tight">A Lei da Vibração</h4>
                                    <p className="text-sm text-white/60">Entenda a sua energia</p>
                                </div>
                            </a>
                        </div>
                    </section>
                </main>
                <nav className="fixed bottom-0 left-0 right-0 w-full bg-background-dark/80 backdrop-blur-lg border-t border-white/10 max-w-md mx-auto">
                    <div className="flex justify-around items-center h-20">
                        <Link to="/home" className="flex flex-col items-center justify-center gap-1 text-gold-soft transition-colors"><Icon name="home" className="text-2xl" filled={true} /><span className="text-xs font-bold">Home</span></Link>
                        <Link to="/meditar" className="flex flex-col items-center justify-center gap-1 text-white/60 hover:text-white transition-colors"><Icon name="self_improvement" className="text-2xl" /><span className="text-xs">Meditar</span></Link>
                        <Link to="/diario" className="flex flex-col items-center justify-center gap-1 text-white/60 hover:text-white transition-colors"><Icon name="edit_note" className="text-2xl" /><span className="text-xs">Diário</span></Link>
                        <Link to="/progresso" className="flex flex-col items-center justify-center gap-1 text-white/60 hover:text-white transition-colors"><Icon name="trending_up" className="text-2xl" /><span className="text-xs">Progresso</span></Link>
                        <Link to="/perfil" className="flex flex-col items-center justify-center gap-1 text-white/60 hover:text-white transition-colors"><Icon name="person" className="text-2xl" /><span className="text-xs">Perfil</span></Link>
                    </div>
                </nav>
            </div>
         );
    };

    const DiarioPage = () => {
        const [entries, setEntries] = useState([
            { id: 'entry_001', date: '20 de Maio de 2024', content: 'Hoje sinto uma imensa gratidão pela minha saúde e pela energia que me permite perseguir os meus sonhos...' },
            { id: 'entry_002', date: '19 de Maio de 2024', content: 'Manifesto abundância e oportunidades incríveis a fluir para a minha vida. Sinto-me próspero e alinhado com o universo.' },
        ]);

        const handleSubmit = (e) => {
            e.preventDefault();
            // Mock functionality: does not actually submit
            alert('Entrada guardada (simulação)!');
        };
        
        return (
            <div className="relative mx-auto flex h-screen max-w-md flex-col overflow-hidden bg-background-light dark:bg-background-dark">
                <header className="flex shrink-0 items-center justify-between p-4">
                    <div className="flex size-12 shrink-0 items-center justify-start"></div>
                    <h1 className="flex-1 text-center text-lg font-bold leading-tight tracking-[-0.015em] text-zinc-900 dark:text-white">O Meu Diário</h1>
                    <div className="flex w-12 items-center justify-end">
                        <button className="flex h-12 cursor-pointer items-center justify-center overflow-hidden rounded-full bg-transparent p-0 text-zinc-600 dark:text-zinc-400">
                            <Icon name="history" className="text-2xl" />
                        </button>
                    </div>
                </header>
                <main className="flex flex-1 flex-col overflow-y-auto px-4 pb-4">
                    <form onSubmit={handleSubmit} className="flex flex-1 flex-col">
                        <div className="mb-4">
                            <div className="flex items-center gap-2">
                                <Icon name="auto_awesome" className="text-base text-accent-gold" />
                                <p className="text-sm font-normal text-zinc-500 dark:text-zinc-400">Formule a sua intenção como se já fosse real.</p>
                            </div>
                        </div>
                        <div className="grid flex-1">
                            <label className="sr-only" htmlFor="journal_content">Registo de hoje</label>
                            <textarea className="form-textarea w-full resize-none overflow-hidden rounded-lg border-none bg-white p-4 text-base font-normal leading-normal text-zinc-900 placeholder:text-zinc-500 focus:outline-0 focus:ring-2 focus:ring-primary/50 dark:bg-zinc-800/50 dark:text-white dark:placeholder:text-zinc-400" id="journal_content" name="content" onInput={(e) => { e.target.style.height = 'auto'; e.target.style.height = (e.target.scrollHeight) + 'px'; }} placeholder="Pelo que é grato hoje? Que realidade está a criar?" required="" rows="10"></textarea>
                        </div>
                        <div className="mt-6">
                            <button className="flex h-14 min-w-[84px] w-full cursor-pointer items-center justify-center gap-2 overflow-hidden rounded-full bg-gradient-to-r from-primary via-[#5f69c3] to-primary px-5 text-base font-bold leading-normal tracking-[0.015em] text-white shadow-lg shadow-primary/30 transition-transform active:scale-95" type="submit">
                                <Icon name="favorite" />
                                <span className="truncate">Guardar e Sentir</span>
                            </button>
                        </div>
                    </form>
                    <div className="mt-8 pt-4 border-t border-zinc-200 dark:border-zinc-800">
                        <h2 className="text-md font-bold text-zinc-700 dark:text-zinc-300 mb-4">Entradas Anteriores</h2>
                        <div className="space-y-3">
                            {entries.map(entry => (
                                <div key={entry.id} className="group flex items-start gap-3 rounded-lg bg-white p-3 dark:bg-zinc-800/50">
                                    <div className="flex-1">
                                        <p className="text-xs text-zinc-500 dark:text-zinc-400">{entry.date}</p>
                                        <p className="line-clamp-2 text-sm text-zinc-800 dark:text-zinc-200 mt-1">{entry.content}</p>
                                    </div>
                                    <div className="flex items-center gap-1 opacity-0 group-hover:opacity-100 transition-opacity">
                                        <button className="p-2 text-zinc-500 hover:text-primary dark:text-zinc-400 dark:hover:text-accent-lavender" title="Editar"><Icon name="edit" className="text-lg" /></button>
                                        <button className="p-2 text-zinc-500 hover:text-red-500 dark:text-zinc-400 dark:hover:text-red-400" title="Eliminar"><Icon name="delete" className="text-lg" /></button>
                                    </div>
                                </div>
                            ))}
                        </div>
                    </div>
                </main>
                <BottomNav />
            </div>
        );
    };

    const ProgressoPage = () => {
        return (
            <div className="relative flex h-auto min-h-screen w-full flex-col text-text-light dark bg-background-dark max-w-md mx-auto">
                <div className="flex-1 pb-24">
                    <div className="sticky top-0 z-10 flex items-center bg-background-dark/80 p-4 pb-2 backdrop-blur-sm">
                        <div className="flex size-12 shrink-0 items-center justify-start">
                            <Icon name="query_stats" className="text-text-light text-3xl" />
                        </div>
                        <h1 className="flex-1 text-center text-lg font-bold leading-tight tracking-[-0.015em] text-text-light">O Meu Progresso</h1>
                        <div className="flex w-12 items-center justify-end">
                            <button className="flex max-w-[480px] cursor-pointer items-center justify-center overflow-hidden rounded-full h-12 bg-transparent text-text-light gap-2 text-base font-bold leading-normal tracking-[0.015em] min-w-0 p-0">
                                <Icon name="calendar_month" className="text-text-light" />
                            </button>
                        </div>
                    </div>
                    <div className="flex flex-wrap gap-4 p-4">
                        <div className="flex min-w-[158px] flex-1 flex-col gap-2 rounded-lg bg-surface-dark p-6 border border-border-dark">
                            <p className="text-text-light/80 text-base font-medium leading-normal">Sessões de Meditação</p>
                            <p className="text-text-light tracking-light text-2xl font-bold leading-tight">128</p>
                        </div>
                        <div className="flex min-w-[158px] flex-1 flex-col gap-2 rounded-lg bg-surface-dark p-6 border border-border-dark">
                            <p className="text-text-light/80 text-base font-medium leading-normal">Dias Consecutivos</p>
                            <p className="text-text-light tracking-light text-2xl font-bold leading-tight">7</p>
                        </div>
                        <div className="flex min-w-[158px] flex-1 flex-col gap-2 rounded-lg bg-surface-dark p-6 border border-border-dark">
                            <p className="text-text-light/80 text-base font-medium leading-normal">Hábitos Concluídos</p>
                            <p className="text-text-light tracking-light text-2xl font-bold leading-tight">82%</p>
                        </div>
                    </div>
                    {/* Chart section */}
                    <div className="flex flex-wrap gap-4 px-4 py-6">
                        <div className="flex min-w-72 flex-1 flex-col gap-2 rounded-lg border border-border-dark bg-surface-dark p-6">
                            <p className="text-text-light text-base font-medium leading-normal">Registo de Humor</p>
                            <p className="text-text-light tracking-light text-[32px] font-bold leading-tight truncate">Estável</p>
                            <div className="flex gap-1">
                                <p className="text-text-light/60 text-base font-normal leading-normal">Últimos 7 dias</p>
                                <p className="text-[#0bda68] text-base font-medium leading-normal">+5%</p>
                            </div>
                            <div className="flex min-h-[180px] flex-1 flex-col gap-8 py-4">
                                <svg fill="none" height="148" preserveAspectRatio="none" viewBox="-3 0 478 150" width="100%" xmlns="http://www.w3.org/2000/svg">
                                    <path d="M0 109C18.1538 109 18.1538 21 36.3077 21C54.4615 21 54.4615 41 72.6154 41C90.7692 41 90.7692 93 108.923 93C127.077 93 127.077 33 145.231 33C163.385 33 163.385 101 181.538 101C199.692 101 199.692 61 217.846 61C236 61 236 45 254.154 45C272.308 45 272.308 121 290.462 121C308.615 121 308.615 149 326.769 149C344.923 149 344.923 1 363.077 1C381.231 1 381.231 81 399.385 81C417.538 81 417.538 129 435.692 129C453.846 129 453.846 25 472 25V149H326.769H0V109Z" fill="url(#paint0_linear_1131_5935)"></path>
                                    <path d="M0 109C18.1538 109 18.1538 21 36.3077 21C54.4615 21 54.4615 41 72.6154 41C90.7692 41 90.7692 93 108.923 93C127.077 93 127.077 33 145.231 33C163.385 33 163.385 101 181.538 101C199.692 101 199.692 61 217.846 61C236 61 236 45 254.154 45C272.308 45 272.308 121 290.462 121C308.615 121 308.615 149 326.769 149C344.923 149 344.923 1 363.077 1C381.231 1 381.231 81 399.385 81C417.538 81 417.538 129 435.692 129C453.846 129 453.846 25 472 25" stroke="#9292c9" strokeLinecap="round" strokeWidth="3"></path>
                                    <defs><linearGradient gradientUnits="userSpaceOnUse" id="paint0_linear_1131_5935" x1="236" x2="236" y1="1" y2="149"><stop stopColor="#4F46E5" stopOpacity="0.5"></stop><stop offset="1" stopColor="#232348" stopOpacity="0"></stop></linearGradient></defs>
                                </svg>
                                <div className="flex justify-around"><p className="text-text-light/60 text-[13px] font-bold leading-normal tracking-[0.015em]">S</p><p className="text-text-light/60 text-[13px] font-bold leading-normal tracking-[0.015em]">T</p><p className="text-text-light/60 text-[13px] font-bold leading-normal tracking-[0.015em]">Q</p><p className="text-text-light/60 text-[13px] font-bold leading-normal tracking-[0.015em]">Q</p><p className="text-text-light/60 text-[13px] font-bold leading-normal tracking-[0.015em]">S</p><p className="text-text-light/60 text-[13px] font-bold leading-normal tracking-[0.015em]">S</p><p className="text-text-light/60 text-[13px] font-bold leading-normal tracking-[0.015em]">D</p></div>
                            </div>
                        </div>
                    </div>
                    {/* Achievements section */}
                    <h2 className="text-text-light text-lg font-bold leading-tight tracking-[-0.015em] px-4 pb-2 pt-4">Conquistas</h2>
                    <div className="flex flex-col gap-3 px-4">
                        <div className="flex items-center gap-4 rounded-lg bg-surface-dark border border-accent p-4">
                            <div className="flex items-center justify-center w-12 h-12 rounded-full bg-accent shrink-0"><Icon name="play_circle" className="text-background-dark text-2xl"/></div>
                            <div className="flex-1"><h3 className="font-bold text-[#F5F5DC]">Primeira Meditação!</h3><p className="text-sm text-text-light/70">Completou a sua primeira sessão.</p><p className="text-xs text-text-light/50 mt-1">Desbloqueado a 23 de Outubro</p></div>
                            <button className="ml-auto flex h-10 items-center justify-center rounded-full bg-[#4F46E5] px-4 text-sm font-bold text-text-light">Ver</button>
                        </div>
                        <div className="flex items-center gap-4 rounded-lg bg-surface-dark border border-border-dark p-4">
                            <div className="flex items-center justify-center w-12 h-12 rounded-full bg-accent shrink-0"><Icon name="local_fire_department" className="text-background-dark text-2xl"/></div>
                            <div className="flex-1"><h3 className="font-bold text-[#F5F5DC]">7 Dias de Foco!</h3><p className="text-sm text-text-light/70">Manteve o foco durante uma semana.</p><p className="text-xs text-text-light/50 mt-1">Desbloqueado a 21 de Outubro</p></div>
                            <Icon name="check_circle" className="text-text-light/80 ml-auto"/>
                        </div>
                    </div>
                </div>
                <BottomNav />
            </div>
        );
    };

    const MeditarPage = () => {
        const categories = ["Ansiedade", "Foco", "Relaxamento", "Sono", "Gratidão"];
        const meditations = [
            { id: 1, title: "Respiração Profunda para Calma", duration: "5 min", type: "Ansiedade", image: "https://lh3.googleusercontent.com/aida-public/AB6AXuDQm86P_Wn-k8S3WqCj4TzI7G6B5Q-P0G5B2A6X8F7T8E9R1A6K3C5B7A0G9I2J4L7M" },
            { id: 2, title: "Mindfulness para Foco Total", duration: "10 min", type: "Foco", image: "https://lh3.googleusercontent.com/aida-public/AB6AXuDQm86P_Wn-k8S3WqCj4TzI7G6B5Q-P0G5B2A6X8F7T8E9R1A6K3C5B7A0G9I2J4L7M" },
            { id: 3, title: "Visualização para um Sono Reparador", duration: "15 min", type: "Sono", image: "https://lh3.googleusercontent.com/aida-public/AB6AXuDQm86P_Wn-k8S3WqCj4TzI7G6B5Q-P0G5B2A6X8F7T8E9R1A6K3C5B7A0G9I2J4L7M" },
            { id: 4, title: "Escaneamento Corporal para Relaxar", duration: "20 min", type: "Relaxamento", image: "https://lh3.googleusercontent.com/aida-public/AB6AXuDQm86P_Wn-k8S3WqCj4TzI7G6B5Q-P0G5B2A6X8F7T8E9R1A6K3C5B7A0G9I2J4L7M" },
            { id: 5, title: "Meditação da Gratidão Infinita", duration: "10 min", type: "Gratidão", image: "https://lh3.googleusercontent.com/aida-public/AB6AXuDQm86P_Wn-k8S3WqCj4TzI7G6B5Q-P0G5B2A6X8F7T8E9R1A6K3C5B7A0G9I2J4L7M" },
        ];

        return (
            <div className="relative flex min-h-screen w-full flex-col bg-background-dark text-white max-w-md mx-auto">
                <header className="p-4 flex flex-col gap-4 sticky top-0 bg-background-dark/80 backdrop-blur-sm z-10">
                    <h1 className="text-white text-2xl font-bold text-center">Meditar</h1>
                    <div className="relative">
                        <input type="search" placeholder="Procurar meditações..." className="w-full bg-surface-dark border border-border-dark rounded-full h-12 pl-12 pr-4 text-white/80 placeholder:text-white/40 focus:outline-none focus:ring-2 focus:ring-primary" />
                        <Icon name="search" className="absolute left-4 top-1/2 -translate-y-1/2 text-white/50" />
                    </div>
                </header>
                <main className="flex-grow px-4 pb-28 space-y-6">
                    <section>
                        <h2 className="text-lg font-bold mb-3">Categorias</h2>
                        <div className="flex gap-3 overflow-x-auto pb-2 -mx-4 px-4 snap-x snap-mandatory">
                            {categories.map(cat => (
                                <button key={cat} className="snap-start shrink-0 px-5 py-2 rounded-full bg-surface-dark border border-border-dark text-sm font-semibold text-white/80 hover:bg-primary/50 transition-colors">{cat}</button>
                            ))}
                        </div>
                    </section>
                    <section>
                        <div className="flex justify-between items-center mb-3">
                            <h2 className="text-lg font-bold">Recomendadas para si</h2>
                            <a className="text-sm font-semibold text-gold-soft" href="#">Ver todas</a>
                        </div>
                        <div className="space-y-3">
                            {meditations.map(med => (
                                <a key={med.id} className="flex items-center bg-card-dark p-3 rounded-xl gap-4 group" href="#">
                                    <img src={med.image} alt={med.title} className="size-16 rounded-lg object-cover" />
                                    <div className="flex-grow">
                                        <h3 className="font-bold text-white">{med.title}</h3>
                                        <p className="text-sm text-white/60">{med.duration} &bull; {med.type}</p>
                                    </div>
                                    <Icon name="play_circle" className="text-white/50 text-3xl group-hover:text-gold-soft transition-colors" />
                                </a>
                            ))}
                        </div>
                    </section>
                </main>
                <BottomNav />
            </div>
        );
    };

    const PerfilPage = () => {
        return (
            <div className="relative mx-auto flex h-auto min-h-screen w-full max-w-md flex-col overflow-x-hidden">
                <header className="flex items-center p-4 pb-2 justify-between sticky top-0 z-10 bg-background-light/80 dark:bg-background-dark/80 backdrop-blur-sm">
                    <div className="flex size-12 shrink-0 items-center justify-start text-zinc-800 dark:text-zinc-200"></div>
                    <h1 className="text-zinc-900 dark:text-white text-lg font-bold leading-tight tracking-[-0.015em] flex-1 text-center">Perfil &amp; Configurações</h1>
                    <div className="flex size-12 shrink-0 items-center justify-end text-zinc-800 dark:text-zinc-200"></div>
                </header>
                <main className="flex-grow px-4 pb-28">
                    <div className="flex w-full flex-col gap-4 items-center pt-4 pb-6">
                        <div className="flex gap-4 flex-col items-center">
                            <div className="relative group">
                                <div className="bg-center bg-no-repeat aspect-square bg-cover rounded-full h-28 w-28 border-2 border-[#303F9F]/50" style={{backgroundImage: `url("https://lh3.googleusercontent.com/aida-public/AB6AXuC5d-oaHVfjKC0MlHBF6kiHYOleWahLQ6-sxF3Cn57Nq82bh7MC60_zzloO8UQmy4u4pi0IxGZyOda3dm3sFt3Ds7OO_FYYIdZ_yNgRfwI0QwMyWQX9uQLQj7OBRkEeBa9CaFXHfua-QGDEY66oaWUYdNfJVx8vnhyAeEzLSswPbrAN-4cNJjSujpSM2uVPBDFZapjfvZfCjilPZ4c3a8DVaZ6XpaAFzdcZbrpCUgn9gPlCEElcNT0mGhoPOpV4S2lu3M7N9rTQ7lU")`}}></div>
                                <label className="absolute inset-0 flex items-center justify-center bg-black/50 rounded-full opacity-0 group-hover:opacity-100 transition-opacity cursor-pointer" htmlFor="profile-photo-upload">
                                    <Icon name="photo_camera" className="text-white text-3xl" />
                                    <input accept="image/*" className="sr-only" id="profile-photo-upload" type="file" />
                                </label>
                            </div>
                            <div className="flex flex-col items-center justify-center">
                                <p className="text-zinc-900 dark:text-white text-[22px] font-bold leading-tight tracking-[-0.015em] text-center">Sofia Almeida</p>
                                <p className="text-zinc-500 dark:text-zinc-400 text-base font-normal leading-normal text-center">Membro desde Janeiro 2024</p>
                            </div>
                        </div>
                        <button className="flex min-w-[84px] cursor-pointer items-center justify-center overflow-hidden rounded-full h-10 px-6 bg-[#303F9F]/10 dark:bg-[#303F9F]/20 text-[#303F9F] dark:text-accent-lavender text-sm font-bold leading-normal tracking-[0.015em] w-full max-w-[240px] mt-2">
                            <span className="truncate">Guardar Alterações</span>
                        </button>
                    </div>
                    <section className="py-4">
                        <h2 className="text-zinc-900 dark:text-white text-lg font-bold leading-tight tracking-[-0.015em] pb-3">Resumo do Progresso</h2>
                        <div className="grid grid-cols-2 gap-3">
                            <div className="flex flex-1 flex-col gap-2 rounded-lg p-4 bg-zinc-200/50 dark:bg-zinc-900/50"><p className="text-zinc-600 dark:text-zinc-300 text-sm font-medium leading-normal">Meditações</p><p className="text-zinc-900 dark:text-white tracking-light text-2xl font-bold leading-tight">82</p></div>
                            <div className="flex flex-1 flex-col gap-2 rounded-lg p-4 bg-zinc-200/50 dark:bg-zinc-900/50"><p className="text-zinc-600 dark:text-zinc-300 text-sm font-medium leading-normal">Dias Meditados</p><p className="text-zinc-900 dark:text-white tracking-light text-2xl font-bold leading-tight">115</p></div>
                            <div className="flex flex-1 flex-col gap-2 rounded-lg p-4 bg-zinc-200/50 dark:bg-zinc-900/50"><p className="text-zinc-600 dark:text-zinc-300 text-sm font-medium leading-normal">Hábitos Hoje</p><p className="text-zinc-900 dark:text-white tracking-light text-2xl font-bold leading-tight">3/5</p></div>
                            <div className="flex flex-1 flex-col gap-2 rounded-lg p-4 bg-zinc-200/50 dark:bg-zinc-900/50"><p className="text-zinc-600 dark:text-zinc-300 text-sm font-medium leading-normal">Entradas Diário</p><p className="text-zinc-900 dark:text-white tracking-light text-2xl font-bold leading-tight">56</p></div>
                        </div>
                    </section>
                    <section className="py-4">
                        <h2 className="text-zinc-900 dark:text-white text-lg font-bold leading-tight tracking-[-0.015em] pb-3">Configurações e Preferências</h2>
                        <div className="flex flex-col gap-2">
                            <div className="flex items-center gap-4 rounded-lg p-4 bg-zinc-200/50 dark:bg-zinc-900/50">
                                <Icon name="language" className="text-accent-gold"/>
                                <span className="flex-grow text-zinc-800 dark:text-zinc-200 font-medium">Idioma</span>
                                <div className="segmented-control flex items-center p-1 rounded-full bg-zinc-300/70 dark:bg-zinc-800">
                                    <input defaultChecked id="lang-pt" name="language" type="radio" value="pt-PT"/>
                                    <label className="px-3 py-1 text-sm font-medium rounded-full cursor-pointer text-zinc-700 dark:text-zinc-300" htmlFor="lang-pt">PT</label>
                                    <input id="lang-en" name="language" type="radio" value="en"/>
                                    <label className="px-3 py-1 text-sm font-medium rounded-full cursor-pointer text-zinc-700 dark:text-zinc-300" htmlFor="lang-en">EN</label>
                                </div>
                            </div>
                        </div>
                    </section>
                </main>
                <BottomNav />
            </div>
        );
    };

    const App = () => {
        return (
            <MemoryRouter>
                <Routes>
                    <Route path="/" element={<WelcomePage />} />
                    <Route path="/home" element={<HomePageV2 />} />
                    <Route path="/home_alt" element={<HomePage />} />
                    <Route path="/diario" element={<DiarioPage />} />
                    <Route path="/progresso" element={<ProgressoPage />} />
                    <Route path="/perfil" element={<PerfilPage />} />
                    <Route path="/meditar" element={<MeditarPage />} />
                </Routes>
            </MemoryRouter>
        );
    };

    const root = createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
