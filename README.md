# Dcord[plataforma_dcord.tsx](https://github.com/user-attachments/files/33917915/plataforma_dcord.tsx)
import React, { useState, useEffect, useRef } from 'react';
import { 
  MessageSquare, Volume2, Mic, MicOff, Headphones, Settings, Plus, Compass, 
  Hash, Bell, Shield, User, LogOut, Smile, Image as ImageIcon, Send, Phone, 
  Video, Users, Check, X, Search, Sparkles, Gamepad2, Code, Music, BookOpen, 
  Tv, Heart, Cpu, Palette, Sword, Trophy, HelpCircle, Sun, Moon, ArrowRight,
  Menu, ChevronDown, Lock, Globe, Paperclip, Share2, RefreshCw, AlertCircle
} from 'lucide-react';

// ==========================================
// CONFIGURAÇÕES DO PROJETO E DADOS INICIAIS
// ==========================================
const APP_CONFIG = {
  name: "Dcord",
  tagline: "Sua plataforma definitiva de comunidades e voz",
  version: "1.0.0"
};

const TEMPLATES = [
  { id: 'gamer', name: 'Comunidade Gamer', icon: Gamepad2, desc: 'Para clãs, guildas e entusiastas de jogos.', category: 'Jogos', channels: ['regras', 'anúncios', 'bate-papo', 'procurando-equipe', 'clips', 'Sala 1', 'Sala 2'] },
  { id: 'dev', name: 'Comunidade de Programação', icon: Code, desc: 'Espaço para desenvolvedores trocarem ideias e códigos.', category: 'Tecnologia', channels: ['dúvidas', 'projetos', 'recursos', 'oportunidades', 'estudo', 'reunião'] },
  { id: 'music', name: 'Comunidade de Música', icon: Music, desc: 'Compartilhe faixas, dicas e colabore em produções.', category: 'Arte', channels: ['recomendações', 'lançamentos', 'instrumentos', 'produção', 'conversa', 'sessões-musicais'] },
  { id: 'study', name: 'Comunidade de Estudos', icon: BookOpen, desc: 'Grupos de estudo, tira-dúvidas e organização acadêmica.', category: 'Educação', channels: ['matérias', 'tarefas', 'materiais', 'organização', 'estudo-coletivo'] },
  { id: 'creators', name: 'Criadores de Conteúdo', icon: Tv, desc: 'Para streamers, YouTubers e criadores de mídia.', category: 'Mídia', channels: ['ideias', 'divulgação', 'edição', 'feedback'] },
  { id: 'anime', name: 'Comunidade de Anime', icon: Sparkles, desc: 'Discussões sobre animes, mangás e cultura pop japonesa.', category: 'Entretenimento', channels: ['novidades', 'recomendações', 'discussões', 'fanart'] },
  { id: 'social', name: 'Comunidade Social', icon: Heart, desc: 'Um lugar relaxante para conversar e fazer amigos.', category: 'Geral', channels: ['apresentações', 'conversas', 'eventos', 'mídias'] },
  { id: 'tech', name: 'Comunidade de Tecnologia', icon: Cpu, desc: 'Gadgets, hardware, software e novidades tech.', category: 'Tecnologia', channels: ['hardware', 'software', 'suporte', 'notícias'] },
  { id: 'art', name: 'Comunidade de Arte', icon: Palette, desc: 'Desenhos, pinturas, design e portfólios criativos.', category: 'Arte', channels: ['desenhos', 'inspirações', 'desafios', 'portfólio'] },
  { id: 'roleplay', name: 'Comunidade de Roleplay', icon: Sword, desc: 'Mundos imaginários, fichas de personagens e histórias.', category: 'Jogos', channels: ['personagens', 'histórias', 'organização', 'eventos'] },
  { id: 'esports', name: 'Comunidade de E-sports', icon: Trophy, desc: 'Competições, escalações e estratégias de campeonatos.', category: 'Jogos', channels: ['campeonatos', 'escalações', 'resultados', 'estratégias'] },
  { id: 'support', name: 'Comunidade de Suporte', icon: HelpCircle, desc: 'Atendimento ao cliente, FAQ e resolução de dúvidas.', category: 'Suporte', channels: ['perguntas', 'soluções'] }
];

const INITIAL_SERVERS = [
  { id: 's1', name: 'Dcord Oficial', icon: '⚡', owner: 'u1', template: 'social', channels: [
      { id: 'c1', name: 'boas-vindas', type: 'text', category: 'Informações' },
      { id: 'c2', name: 'geral', type: 'text', category: 'Bate-papo' },
      { id: 'c3', name: 'memes', type: 'text', category: 'Bate-papo' },
      { id: 'c4', name: 'Lobby de Voz', type: 'voice', category: 'Canais de Voz' }
    ]
  },
  { id: 's2', name: 'Gamer Lounge BR', icon: '🎮', owner: 'u2', template: 'gamer', channels: [
      { id: 'c5', name: 'geral-games', type: 'text', category: 'Gamer' },
      { id: 'c6', name: 'clips-e-highlights', type: 'text', category: 'Gamer' },
      { id: 'c7', name: 'Esquadrão 1', type: 'voice', category: 'Voz' }
    ]
  }
];

const INITIAL_FRIENDS = [
  { id: 'f1', name: 'Mariana Costa', tag: 'mari02', status: 'online', activity: 'Jogando Valorant', avatar: 'https://images.unsplash.com/photo-1494790108377-be9c29b29330?w=150' },
  { id: 'f2', name: 'Carlos Eduardo', tag: 'cadu99', status: 'idle', activity: 'Ouvindo Spotify', avatar: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=150' },
  { id: 'f3', name: 'Beatriz Lima', tag: 'biatriz', status: 'dnd', activity: 'Codando em React', avatar: 'https://images.unsplash.com/photo-1438761681033-6461ffad8d80?w=150' },
  { id: 'f4', name: 'Lucas Souza', tag: 'lucas_s', status: 'offline', activity: '', avatar: 'https://images.unsplash.com/photo-1500648767791-00dcc994a43e?w=150' }
];

// ==========================================
// COMPONENTE PRINCIPAL
// ==========================================
export default function App() {
  // Estado de Autenticação ('landing', 'login', 'register', 'app')
  const [authView, setAuthView] = useState('landing');
  const [user, setUser] = useState(null);

  // Configurações de Tema e UI
  const [theme, setTheme] = useState('dark'); // 'dark' | 'light'
  const [activeNav, setActiveNav] = useState('@me'); // '@me' ou serverId
  const [activeChannelId, setActiveChannelId] = useState('friends'); // 'friends' ou channelId
  const [activeFriendTab, setActiveFriendTab] = useState('online'); // online, all, pending, blocked, add
  
  // Modais
  const [settingsOpen, setSettingsOpen] = useState(false);
  const [createServerOpen, setCreateServerOpen] = useState(false);
  const [exploreOpen, setExploreOpen] = useState(false);
  const [activeSettingsTab, setActiveSettingsTab] = useState('account');

  // Estado de Dados
  const [servers, setServers] = useState(INITIAL_SERVERS);
  const [friends, setFriends] = useState(INITIAL_FRIENDS);
  const [messages, setMessages] = useState({
    'c2': [
      { id: 'm1', user: 'Mariana Costa', text: 'Fala galera! Bem-vindos ao Dcord 🚀', time: '10:42', avatar: 'https://images.unsplash.com/photo-1494790108377-be9c29b29330?w=150' },
      { id: 'm2', user: 'Carlos Eduardo', text: 'Plataforma super rápida e moderna!', time: '10:43', avatar: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=150' }
    ]
  });
  const [inputMessage, setInputMessage] = useState('');

  // Estado de Voz
  const [inVoiceChannel, setInVoiceChannel] = useState(null);
  const [isMuted, setIsMuted] = useState(false);
  const [isDeafened, setIsDeafened] = useState(false);

  // ==========================================
  // RENDERIZAÇÃO CONDICIONAL DE TELAS
  // ==========================================
  if (authView === 'landing' && !user) {
    return <LandingPage onNavigate={setAuthView} theme={theme} setTheme={setTheme} />;
  }
  if ((authView === 'login' || authView === 'register') && !user) {
    return <AuthScreen mode={authView} onNavigate={setAuthView} onLogin={(u) => { setUser(u); setAuthView('app'); }} theme={theme} setTheme={setTheme} />;
  }

  // ==========================================
  // APLICAÇÃO PRINCIPAL (DISCORD CLONE - DCORD)
  // ==========================================
  const activeServer = servers.find(s => s.id === activeNav);
  const activeChannel = activeServer?.channels.find(c => c.id === activeChannelId);

  const handleSendMessage = (e) => {
    e.preventDefault();
    if (!inputMessage.trim()) return;
    const targetChannel = activeChannelId;
    const newMsg = {
      id: Date.now().toString(),
      user: user.name,
      text: inputMessage,
      time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
      avatar: user.avatar || 'https://images.unsplash.com/photo-1535713875002-d1d0cf377fde?w=150'
    };
    setMessages(prev => ({
      ...prev,
      [targetChannel]: [...(prev[targetChannel] || []), newMsg]
    }));
    setInputMessage('');
  };

  const handleCreateServer = (serverData) => {
    const newS = {
      id: 's_' + Date.now(),
      name: serverData.name,
      icon: serverData.icon || '🚀',
      owner: user.id,
      template: serverData.template,
      channels: [
        { id: 'c_' + Date.now() + 1, name: 'geral', type: 'text', category: 'Texto' },
        { id: 'c_' + Date.now() + 2, name: 'Canal de Voz 1', type: 'voice', category: 'Voz' }
      ]
    };
    setServers([...servers, newS]);
    setCreateServerOpen(false);
    setActiveNav(newS.id);
    setActiveChannelId(newS.channels[0].id);
  };

  return (
    <div className={`flex h-screen w-screen overflow-hidden select-none ${theme === 'dark' ? 'bg-[#313338] text-[#dbdee1]' : 'bg-[#f2f3f5] text-[#23272a]'}`}>
      
      {/* 1. BARRA LATERAL DE SERVIDORES */}
      <nav className={`w-[72px] flex flex-col items-center py-3 gap-2 shrink-0 z-20 ${theme === 'dark' ? 'bg-[#1e1f22]' : 'bg-[#e3e5e8]'}`}>
        {/* Botão Direct Messages / Home */}
        <button 
          onClick={() => { setActiveNav('@me'); setActiveChannelId('friends'); }}
          className={`relative group flex items-center justify-center w-12 h-12 rounded-[24px] hover:rounded-[16px] transition-all duration-200 ${activeNav === '@me' ? 'rounded-[16px] bg-[#5865f2] text-white' : theme === 'dark' ? 'bg-[#313338] hover:bg-[#5865f2] hover:text-white text-[#dbdee1]' : 'bg-white hover:bg-[#5865f2] hover:text-white text-[#313338]'}`}
          title={APP_CONFIG.name}
        >
          <span className="font-bold text-lg">⚡</span>
          <div className={`absolute left-0 w-1 bg-white rounded-r transition-all duration-200 ${activeNav === '@me' ? 'h-10' : 'h-0 group-hover:h-5'}`} />
        </button>

        <div className={`w-8 h-[2px] rounded ${theme === 'dark' ? 'bg-[#35363c]' : 'bg-[#d1d5db]'}`} />

        {/* Lista de Servidores */}
        <div className="flex-1 w-full overflow-y-auto overflow-x-hidden flex flex-col items-center gap-2">
          {servers.map(server => (
            <button
              key={server.id}
              onClick={() => { setActiveNav(server.id); setActiveChannelId(server.channels[0]?.id); }}
              className={`relative group flex items-center justify-center w-12 h-12 rounded-[24px] hover:rounded-[16px] transition-all duration-200 text-xl font-bold ${activeNav === server.id ? 'rounded-[16px] bg-[#5865f2] text-white' : theme === 'dark' ? 'bg-[#313338] hover:bg-[#5865f2] hover:text-white text-[#dbdee1]' : 'bg-white hover:bg-[#5865f2] hover:text-white text-[#23272a]'}`}
              title={server.name}
            >
              {server.icon}
              <div className={`absolute left-0 w-1 bg-white rounded-r transition-all duration-200 ${activeNav === server.id ? 'h-10' : 'h-0 group-hover:h-5'}`} />
            </button>
          ))}

          {/* Botão Adicionar Servidor */}
          <button 
            onClick={() => setCreateServerOpen(true)}
            className={`flex items-center justify-center w-12 h-12 rounded-[24px] hover:rounded-[16px] transition-all duration-200 text-[#23a55a] hover:bg-[#23a55a] hover:text-white ${theme === 'dark' ? 'bg-[#2b2d31]' : 'bg-white'}`}
            title="Adicionar Servidor"
          >
            <Plus size={24} />
          </button>

          {/* Botão Explorar Comunidades */}
          <button 
            onClick={() => setExploreOpen(true)}
            className={`flex items-center justify-center w-12 h-12 rounded-[24px] hover:rounded-[16px] transition-all duration-200 text-[#23a55a] hover:bg-[#23a55a] hover:text-white ${theme === 'dark' ? 'bg-[#2b2d31]' : 'bg-white'}`}
            title="Explorar Comunidades"
          >
            <Compass size={24} />
          </button>
        </div>

        {/* Perfil Mini / Configurações Rápidas na barra lateral */}
        <button 
          onClick={() => setSettingsOpen(true)}
          className={`flex items-center justify-center w-12 h-12 rounded-full transition-all hover:bg-zinc-700/50 relative`}
          title="Configurações"
        >
          <img src={user.avatar} alt="Avatar" className="w-10 h-10 rounded-full object-cover" />
          <span className="absolute bottom-0 right-0 w-3.5 h-3.5 bg-[#23a55a] border-2 border-[#1e1f22] rounded-full"></span>
        </button>
      </nav>

      {/* 2. PAINEL SECUNDÁRIO (Canais ou Amigos) */}
      <aside className={`w-60 flex flex-col shrink-0 ${theme === 'dark' ? 'bg-[#2b2d31]' : 'bg-[#f0f2f5]'}`}>
        {activeNav === '@me' ? (
          // MENU DE AMIGOS / DM
          <div className="flex flex-col h-full">
            <div className={`h-12 px-4 flex items-center shadow-sm border-b font-semibold ${theme === 'dark' ? 'border-[#1f2023]' : 'border-[#e0e2e5]'}`}>
              <button className={`w-full text-left px-2 py-1.5 rounded text-sm font-medium ${theme === 'dark' ? 'bg-[#1e1f22] text-[#949ba4]' : 'bg-[#e3e5e8] text-[#6a7480]'}`}>
                Encontrar ou iniciar uma conversa
              </button>
            </div>
            <div className="flex-1 overflow-y-auto px-2 py-3 space-y-0.5">
              <button 
                onClick={() => setActiveChannelId('friends')}
                className={`w-full flex items-center gap-3 px-3 py-2.5 rounded text-sm font-medium transition-colors ${activeChannelId === 'friends' ? (theme === 'dark' ? 'bg-[#404249] text-white' : 'bg-[#e2e8f0] text-black') : (theme === 'dark' ? 'text-[#949ba4] hover:bg-[#35373c] hover:text-[#dbdee1]' : 'text-[#6a7480] hover:bg-[#e6e8eb]')}`}
              >
                <Users size={20} /> Amigos
              </button>
              
              <div className="pt-4 pb-1 px-3 text-xs font-bold uppercase tracking-wider text-[#949ba4]">
                Mensagens Diretas
              </div>
              {friends.map(f => (
                <button
                  key={f.id}
                  onClick={() => setActiveChannelId('dm_' + f.id)}
                  className={`w-full flex items-center gap-3 px-2 py-2 rounded text-sm font-medium transition-colors group ${activeChannelId === 'dm_' + f.id ? (theme === 'dark' ? 'bg-[#404249] text-white' : 'bg-[#e2e8f0] text-black') : (theme === 'dark' ? 'text-[#949ba4] hover:bg-[#35373c] hover:text-[#dbdee1]' : 'text-[#6a7480] hover:bg-[#e6e8eb]')}`}
                >
                  <div className="relative">
                    <img src={f.avatar} alt={f.name} className="w-8 h-8 rounded-full object-cover" />
                    <span className={`absolute bottom-0 right-0 w-2.5 h-2.5 border-2 rounded-full ${f.status === 'online' ? 'bg-[#23a55a]' : f.status === 'idle' ? 'bg-[#f0b232]' : f.status === 'dnd' ? 'bg-[#f23f43]' : 'bg-[#80848e]'}`} />
                  </div>
                  <div className="flex flex-col text-left truncate">
                    <span className="truncate">{f.name}</span>
                    <span className="text-xs text-[#949ba4] truncate">{f.activity}</span>
                  </div>
                </button>
              ))}
            </div>
          </div>
        ) : (
          // MENU DO SERVIDOR (Canais)
          <div className="flex flex-col h-full">
            <div className={`h-12 px-4 flex items-center justify-between shadow-sm border-b font-bold ${theme === 'dark' ? 'border-[#1f2023]' : 'border-[#e0e2e5]'}`}>
              <span className="truncate">{activeServer?.name}</span>
              <ChevronDown size={18} />
            </div>
            
            <div className="flex-1 overflow-y-auto px-2 py-3 space-y-4">
              {/* Canais de Texto */}
              <div>
                <div className="flex items-center justify-between px-1 mb-1 text-xs font-bold uppercase tracking-wider text-[#949ba4]">
                  <span>Canais de Texto</span>
                  <Plus size={16} className="cursor-pointer hover:text-white" />
                </div>
                <div className="space-y-0.5">
                  {activeServer?.channels.filter(c => c.type === 'text').map(channel => (
                    <button
                      key={channel.id}
                      onClick={() => setActiveChannelId(channel.id)}
                      className={`w-full flex items-center gap-2 px-2 py-1.5 rounded text-sm font-medium transition-colors ${activeChannelId === channel.id ? (theme === 'dark' ? 'bg-[#404249] text-white' : 'bg-[#e2e8f0] text-black') : (theme === 'dark' ? 'text-[#949ba4] hover:bg-[#35373c] hover:text-[#dbdee1]' : 'text-[#6a7480] hover:bg-[#e6e8eb]')}`}
                    >
                      <Hash size={18} className="text-[#80848e]" />
                      <span className="truncate">{channel.name}</span>
                    </button>
                  ))}
                </div>
              </div>

              {/* Canais de Voz */}
              <div>
                <div className="flex items-center justify-between px-1 mb-1 text-xs font-bold uppercase tracking-wider text-[#949ba4]">
                  <span>Canais de Voz</span>
                  <Plus size={16} className="cursor-pointer hover:text-white" />
                </div>
                <div className="space-y-0.5">
                  {activeServer?.channels.filter(c => c.type === 'voice').map(channel => (
                    <button
                      key={channel.id}
                      onClick={() => setInVoiceChannel(channel.name)}
                      className={`w-full flex items-center gap-2 px-2 py-1.5 rounded text-sm font-medium transition-colors ${inVoiceChannel === channel.name ? 'bg-[#23a55a]/20 text-[#23a55a]' : theme === 'dark' ? 'text-[#949ba4] hover:bg-[#35373c] hover:text-[#dbdee1]' : 'text-[#6a7480] hover:bg-[#e6e8eb]'}`}
                    >
                      <Volume2 size={18} className={inVoiceChannel === channel.name ? 'text-[#23a55a]' : 'text-[#80848e]'} />
                      <span className="truncate">{channel.name}</span>
                    </button>
                  ))}
                </div>
              </div>
            </div>
          </div>
        )}

        {/* Rodapé de Conexão de Voz Ativa */}
        {inVoiceChannel && (
          <div className={`p-2 border-t flex items-center justify-between ${theme === 'dark' ? 'bg-[#1a1b1e] border-[#1f2023]' : 'bg-[#e0e2e5] border-[#d1d5db]'}`}>
            <div className="flex items-center gap-2 truncate">
              <div className="w-2 h-2 rounded-full bg-[#23a55a] animate-pulse" />
              <div className="text-xs truncate">
                <p className="font-bold text-[#23a55a]">Voz Conectada</p>
                <p className="text-[#949ba4]">{inVoiceChannel}</p>
              </div>
            </div>
            <div className="flex items-center gap-1">
              <button onClick={() => setIsMuted(!isMuted)} className={`p-1.5 rounded hover:bg-zinc-700/40 ${isMuted ? 'text-[#f23f43]' : ''}`}>
                {isMuted ? <MicOff size={16} /> : <Mic size={16} />}
              </button>
              <button onClick={() => setInVoiceChannel(null)} className="p-1.5 rounded hover:bg-zinc-700/40 text-[#f23f43]" title="Desconectar">
                <Phone size={16} className="rotate-[135deg]" />
              </button>
            </div>
          </div>
        )}

        {/* Barra do Usuário Atual */}
        <div className={`p-2 flex items-center justify-between ${theme === 'dark' ? 'bg-[#232428]' : 'bg-[#ebedef]'}`}>
          <div className="flex items-center gap-2 truncate">
            <img src={user.avatar} alt="Avatar" className="w-8 h-8 rounded-full object-cover" />
            <div className="flex flex-col truncate text-xs">
              <span className="font-bold truncate">{user.name}</span>
              <span className="text-[#949ba4] truncate">#{user.tag || '0001'}</span>
            </div>
          </div>
          <div className="flex items-center gap-1">
            <button onClick={() => setIsMuted(!isMuted)} className="p-1.5 rounded hover:bg-zinc-500/20 text-[#949ba4] hover:text-white">
              {isMuted ? <MicOff size={16} className="text-[#f23f43]" /> : <Mic size={16} />}
            </button>
            <button onClick={() => setSettingsOpen(true)} className="p-1.5 rounded hover:bg-zinc-500/20 text-[#949ba4] hover:text-white">
              <Settings size={16} />
            </button>
          </div>
        </div>
      </aside>

      {/* 3. PAINEL PRINCIPAL (Conteúdo / Chat / Amigos) */}
      <main className={`flex-1 flex flex-col ${theme === 'dark' ? 'bg-[#313338]' : 'bg-white'}`}>
        
        {/* Topo / Cabeçalho */}
        <header className={`h-12 px-4 flex items-center justify-between shadow-sm border-b shrink-0 ${theme === 'dark' ? 'border-[#1f2023]' : 'border-[#e0e2e5]'}`}>
          <div className="flex items-center gap-2 font-bold">
            {activeNav === '@me' ? (
              <div className="flex items-center gap-2">
                <Users size={20} className="text-[#80848e]" />
                <span>Amigos</span>
              </div>
            ) : (
              <div className="flex items-center gap-2">
                <Hash size={20} className="text-[#80848e]" />
                <span>{activeChannel?.name || 'geral'}</span>
              </div>
            )}
          </div>
          <div className="flex items-center gap-4 text-[#949ba4]">
            <Bell size={20} className="cursor-pointer hover:text-white" />
            <div className="relative flex items-center">
              <input type="text" placeholder="Buscar" className={`text-xs px-2 py-1 rounded w-36 focus:w-48 transition-all outline-none ${theme === 'dark' ? 'bg-[#1e1f22] text-white' : 'bg-[#e3e5e8] text-black'}`} />
              <Search size={14} className="absolute right-2 text-[#949ba4]" />
            </div>
          </div>
        </header>

        {/* Área de Conteúdo Central */}
        <div className="flex-1 flex overflow-hidden">
          
          {activeNav === '@me' && activeChannelId === 'friends' ? (
            // TELA DE AMIGOS
            <div className="flex-1 flex flex-col p-6 overflow-y-auto">
              <div className="flex gap-4 border-b pb-4 border-zinc-700/30">
                {['online', 'all', 'pending', 'blocked', 'add'].map(tab => (
                  <button 
                    key={tab}
                    onClick={() => setActiveFriendTab(tab)}
                    className={`px-3 py-1 rounded text-sm font-semibold capitalize ${activeFriendTab === tab ? 'bg-zinc-600/30 text-white' : 'text-[#949ba4] hover:text-white'}`}
                  >
                    {tab === 'online' ? 'Online' : tab === 'all' ? 'Todos' : tab === 'pending' ? 'Pendentes' : tab === 'blocked' ? 'Bloqueados' : 'Adicionar Amigo'}
                  </button>
                ))}
              </div>

              <div className="mt-6 flex-1">
                <h3 className="text-xs font-bold uppercase tracking-wider text-[#949ba4] mb-4">
                  {activeFriendTab === 'online' ? `Online — ${friends.filter(f => f.status !== 'offline').length}` : 'Lista de Amigos'}
                </h3>
                <div className="space-y-2">
                  {friends.filter(f => activeFriendTab === 'online' ? f.status !== 'offline' : true).map(f => (
                    <div key={f.id} className={`flex items-center justify-between p-3 rounded-lg border transition-all ${theme === 'dark' ? 'border-[#3f4147]/50 hover:bg-[#35373c]' : 'border-zinc-200 hover:bg-zinc-100'}`}>
                      <div className="flex items-center gap-3">
                        <div className="relative">
                          <img src={f.avatar} alt={f.name} className="w-10 h-10 rounded-full object-cover" />
                          <span className={`absolute bottom-0 right-0 w-3 h-3 border-2 rounded-full ${f.status === 'online' ? 'bg-[#23a55a]' : 'bg-[#80848e]'}`} />
                        </div>
                        <div>
                          <p className="font-bold">{f.name} <span className="text-xs text-[#949ba4] font-normal">#{f.tag}</span></p>
                          <p className="text-xs text-[#949ba4]">{f.activity || f.status}</p>
                        </div>
                      </div>
                      <div className="flex gap-2">
                        <button onClick={() => setActiveChannelId('dm_' + f.id)} className="p-2 rounded-full bg-[#2b2d31] hover:bg-[#111214] text-white" title="Mensagem">
                          <MessageSquare size={18} />
                        </button>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          ) : (
            // CHAT DE TEXTO (Servidor ou DM)
            <div className="flex-1 flex flex-col justify-between overflow-hidden">
              <div className="flex-1 overflow-y-auto p-4 space-y-4">
                <div className="flex flex-col items-center justify-center py-10 border-b border-zinc-700/30 mb-4 text-center">
                  <div className="w-16 h-16 rounded-full bg-[#5865f2] flex items-center justify-center text-white text-3xl font-bold mb-3">
                    #
                  </div>
                  <h2 className="text-2xl font-bold">Bem-vindo(a) ao canal!</h2>
                  <p className="text-sm text-[#949ba4] mt-1">Este é o início da conversa neste canal.</p>
                </div>

                {/* Lista de Mensagens */}
                {(messages[activeChannelId] || []).map(msg => (
                  <div key={msg.id} className="flex gap-4 items-start group hover:bg-zinc-500/5 p-1 rounded">
                    <img src={msg.avatar} alt={msg.user} className="w-10 h-10 rounded-full object-cover mt-0.5" />
                    <div className="flex-1">
                      <div className="flex items-baseline gap-2">
                        <span className="font-semibold text-sm hover:underline cursor-pointer">{msg.user}</span>
                        <span className="text-xs text-[#949ba4]">{msg.time}</span>
                      </div>
                      <p className="text-sm mt-0.5">{msg.text}</p>
                    </div>
                  </div>
                ))}
              </div>

              {/* Caixa de Entrada de Mensagem */}
              <div className="p-4 pt-0">
                <form onSubmit={handleSendMessage} className={`flex items-center rounded-lg px-4 py-2.5 ${theme === 'dark' ? 'bg-[#383a40]' : 'bg-[#ebedef]'}`}>
                  <button type="button" className="text-[#b5bac1] hover:text-white mr-3">
                    <Plus size={20} className="bg-[#4e5058] rounded-full p-0.5 text-white" />
                  </button>
                  <input
                    type="text"
                    value={inputMessage}
                    onChange={(e) => setInputMessage(e.target.value)}
                    placeholder={`Conversar em #${activeChannel?.name || 'geral'}`}
                    className="flex-1 bg-transparent text-sm outline-none placeholder-[#80848e]"
                  />
                  <div className="flex items-center gap-3 text-[#b5bac1]">
                    <Smile size={22} className="cursor-pointer hover:text-white" />
                    <Send size={20} className="cursor-pointer hover:text-white text-[#5865f2]" onClick={handleSendMessage} />
                  </div>
                </form>
              </div>
            </div>
          )}
        </div>
      </main>

      {/* ========================================== */}
      // MODAL DE CONFIGURAÇÕES
      // ==========================================
      {settingsOpen && (
        <div className="fixed inset-0 z-50 flex bg-[#313338] animate-fade-in">
          <div className={`w-60 p-6 flex flex-col gap-2 border-r ${theme === 'dark' ? 'bg-[#2b2d31] border-[#1f2023]' : 'bg-[#f0f2f5] border-[#d1d5db]'}`}>
            <h3 className="text-xs font-bold uppercase tracking-wider text-[#949ba4] px-2 mb-2">Configurações de Usuário</h3>
            <button 
              onClick={() => setActiveSettingsTab('account')}
              className={`text-left px-3 py-2 rounded text-sm font-medium ${activeSettingsTab === 'account' ? 'bg-[#404249] text-white' : 'text-[#949ba4] hover:bg-[#35373c]'}`}
            >
              Minha Conta
            </button>
            <button 
              onClick={() => setActiveSettingsTab('appearance')}
              className={`text-left px-3 py-2 rounded text-sm font-medium ${activeSettingsTab === 'appearance' ? 'bg-[#404249] text-white' : 'text-[#949ba4] hover:bg-[#35373c]'}`}
            >
              Aparência & Tema
            </button>
            <div className="my-2 border-t border-zinc-700/30" />
            <button 
              onClick={() => { setUser(null); setAuthView('landing'); setSettingsOpen(false); }}
              className="text-left px-3 py-2 rounded text-sm font-medium text-[#f23f43] hover:bg-[#f23f43]/10 flex items-center gap-2"
            >
              <LogOut size={16} /> Sair da Conta
            </button>
          </div>

          <div className="flex-1 p-10 overflow-y-auto relative">
            <button onClick={() => setSettingsOpen(false)} className="absolute top-10 right-10 p-2 rounded-full border border-zinc-500 text-zinc-400 hover:text-white">
              <X size={20} />
            </button>

            {activeSettingsTab === 'account' ? (
              <div className="max-w-xl space-y-6">
                <h2 className="text-xl font-bold">Minha Conta</h2>
                <div className={`p-4 rounded-lg flex items-center justify-between ${theme === 'dark' ? 'bg-[#1e1f22]' : 'bg-zinc-100'}`}>
                  <div className="flex items-center gap-4">
                    <img src={user.avatar} alt="Avatar" className="w-20 h-20 rounded-full object-cover" />
                    <div>
                      <h3 className="font-bold text-lg">{user.name}</h3>
                      <p className="text-sm text-[#949ba4]">#{user.tag || '0001'}</p>
                    </div>
                  </div>
                  <button className="px-4 py-2 bg-[#5865f2] hover:bg-[#4752c4] text-white rounded text-sm font-medium">Editar Perfil</button>
                </div>
              </div>
            ) : (
              <div className="max-w-xl space-y-6">
                <h2 className="text-xl font-bold">Aparência</h2>
                <p className="text-sm text-[#949ba4]">Escolha como a interface do {APP_CONFIG.name} se parece para você.</p>
                <div className="flex gap-4">
                  <button 
                    onClick={() => setTheme('dark')} 
                    className={`flex-1 p-4 rounded-lg border-2 flex flex-col items-center gap-3 ${theme === 'dark' ? 'border-[#5865f2] bg-[#2b2d31]' : 'border-zinc-300'}`}
                  >
                    <Moon size={28} />
                    <span className="font-bold">Escuro (Dark)</span>
                  </button>
                  <button 
                    onClick={() => setTheme('light')} 
                    className={`flex-1 p-4 rounded-lg border-2 flex flex-col items-center gap-3 ${theme === 'light' ? 'border-[#5865f2] bg-white text-black' : 'border-zinc-300'}`}
                  >
                    <Sun size={28} />
                    <span className="font-bold">Claro (Light)</span>
                  </button>
                </div>
              </div>
            )}
          </div>
        </div>
      )}

      {/* ========================================== */}
      // MODAL DE CRIAR SERVIDOR (COM TEMPLATES)
      // ==========================================
      {createServerOpen && (
        <CreateServerModal 
          onClose={() => setCreateServerOpen(false)} 
          onCreate={handleCreateServer} 
          theme={theme} 
        />
      )}

      {/* ========================================== */}
      // MODAL DE EXPLORAR COMUNIDADES
      // ==========================================
      {exploreOpen && (
        <div className="fixed inset-0 z-50 flex items-center justify-center bg-black/70 backdrop-blur-sm p-4 animate-fade-in">
          <div className={`w-full max-w-3xl rounded-xl p-6 flex flex-col max-h-[80vh] ${theme === 'dark' ? 'bg-[#313338] text-white' : 'bg-white text-black'}`}>
            <div className="flex justify-between items-center mb-6">
              <h2 className="text-2xl font-bold flex items-center gap-2"><Compass className="text-[#5865f2]" /> Descobrir Comunidades</h2>
              <button onClick={() => setExploreOpen(false)} className="text-zinc-400 hover:text-white"><X size={24} /></button>
            </div>
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4 overflow-y-auto">
              {TEMPLATES.map(t => (
                <div key={t.id} className={`p-4 rounded-lg border flex flex-col justify-between ${theme === 'dark' ? 'bg-[#2b2d31] border-zinc-700/50' : 'bg-zinc-50 border-zinc-200'}`}>
                  <div>
                    <div className="flex items-center gap-3 mb-2">
                      <div className="w-10 h-10 rounded-lg bg-[#5865f2]/20 text-[#5865f2] flex items-center justify-center font-bold">
                        <t.icon size={22} />
                      </div>
                      <div>
                        <h4 className="font-bold">{t.name}</h4>
                        <span className="text-xs text-[#949ba4]">{t.category}</span>
                      </div>
                    </div>
                    <p className="text-xs text-[#949ba4] mt-2">{t.desc}</p>
                  </div>
                  <button 
                    onClick={() => {
                      handleCreateServer({ name: t.name, template: t.id, icon: '🌟' });
                      setExploreOpen(false);
                    }}
                    className="mt-4 w-full py-2 bg-[#5865f2] hover:bg-[#4752c4] text-white rounded text-sm font-medium transition-colors"
                  >
                    Criar com este Template
                  </button>
                </div>
              ))}
            </div>
          </div>
        </div>
      )}

    </div>
  );
}

// ==========================================
// COMPONENTE: MODAL DE CRIAÇÃO DE SERVIDOR
// ==========================================
function CreateServerModal({ onClose, onCreate, theme }) {
  const [step, setStep] = useState('choose'); // 'choose' | 'customize'
  const [selectedTemplate, setSelectedTemplate] = useState(null);
  const [serverName, setServerName] = useState('Meu Servidor');

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center bg-black/70 backdrop-blur-sm p-4 animate-fade-in">
      <div className={`w-full max-w-lg rounded-xl p-6 shadow-2xl ${theme === 'dark' ? 'bg-[#313338] text-white' : 'bg-white text-black'}`}>
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-2xl font-bold text-center flex-1">Criar seu Servidor</h2>
          <button onClick={onClose} className="text-zinc-400 hover:text-white"><X size={20} /></button>
        </div>

        {step === 'choose' ? (
          <div className="space-y-4">
            <p className="text-sm text-center text-[#949ba4]">O seu servidor é onde você e seus amigos conversam. Escolha um template para começar ou crie do zero.</p>
            <div className="space-y-2 max-h-60 overflow-y-auto">
              <button 
                onClick={() => { setSelectedTemplate(null); setServerName('Servidor do ' + 'Usuário'); setStep('customize'); }}
                className={`w-full p-3 rounded-lg border text-left font-semibold flex items-center justify-between ${theme === 'dark' ? 'border-zinc-700 hover:bg-[#35373c]' : 'border-zinc-200 hover:bg-zinc-100'}`}
              >
                <span>Criar do Zero</span>
                <ArrowRight size={18} />
              </button>
              {TEMPLATES.map(t => (
                <button 
                  key={t.id}
                  onClick={() => { setSelectedTemplate(t.id); setServerName(t.name); setStep('customize'); }}
                  className={`w-full p-3 rounded-lg border text-left flex items-center justify-between ${theme === 'dark' ? 'border-zinc-700 hover:bg-[#35373c]' : 'border-zinc-200 hover:bg-zinc-100'}`}
                >
                  <div className="flex items-center gap-3">
                    <t.icon size={20} className="text-[#5865f2]" />
                    <div>
                      <p className="font-bold text-sm">{t.name}</p>
                      <p className="text-xs text-[#949ba4]">{t.desc}</p>
                    </div>
                  </div>
                  <ArrowRight size={18} className="text-[#949ba4]" />
                </button>
              ))}
            </div>
          </div>
        ) : (
          <div className="space-y-4">
            <p className="text-sm text-center text-[#949ba4]">Personalize o nome e o ícone do seu novo servidor.</p>
            <div>
              <label className="text-xs font-bold uppercase tracking-wider text-[#949ba4] block mb-2">Nome do Servidor</label>
              <input 
                type="text" 
                value={serverName} 
                onChange={(e) => setServerName(e.target.value)} 
                className={`w-full p-3 rounded text-sm outline-none ${theme === 'dark' ? 'bg-[#1e1f22] text-white' : 'bg-zinc-100 text-black'}`}
              />
            </div>
            <div className="flex justify-between items-center pt-4">
              <button onClick={() => setStep('choose')} className="text-sm hover:underline">Voltar</button>
              <button 
                onClick={() => onCreate({ name: serverName, template: selectedTemplate })}
                className="px-6 py-2.5 bg-[#5865f2] hover:bg-[#4752c4] text-white rounded font-medium text-sm transition-colors"
              >
                Criar
              </button>
            </div>
          </div>
        )}
      </div>
    </div>
  );
}

// ==========================================
// COMPONENTE: PÁGINA INICIAL (LANDING PAGE)
// ==========================================
function LandingPage({ onNavigate, theme, setTheme }) {
  return (
    <div className={`min-h-screen flex flex-col ${theme === 'dark' ? 'bg-[#1e1f22] text-white' : 'bg-white text-black'}`}>
      <header className="px-8 py-4 flex justify-between items-center max-w-7xl mx-auto w-full">
        <div className="flex items-center gap-2 text-2xl font-bold text-[#5865f2]">
          <span className="bg-[#5865f2] text-white p-1 rounded-lg">⚡</span> {APP_CONFIG.name}
        </div>
        <div className="flex items-center gap-4">
          <button onClick={() => setTheme(theme === 'dark' ? 'light' : 'dark')} className="p-2 rounded-full hover:bg-zinc-500/10">
            {theme === 'dark' ? <Sun size={20} /> : <Moon size={20} />}
          </button>
          <button onClick={() => onNavigate('login')} className="px-4 py-2 rounded-full bg-[#5865f2] text-white font-medium hover:bg-[#4752c4] transition-colors">
            Entrar
          </button>
        </div>
      </header>

      <section className="flex-1 flex flex-col items-center justify-center text-center px-4 py-20 max-w-4xl mx-auto">
        <span className="px-4 py-1.5 rounded-full bg-[#5865f2]/10 text-[#5865f2] text-xs font-bold uppercase tracking-wider mb-6">
          Inspirado no Discord • Feito para Comunidades
        </span>
        <h1 className="text-5xl md:text-7xl font-extrabold tracking-tight mb-6">
          Onde você pertence ao seu próprio <span className="text-[#5865f2]">espaço</span>
        </h1>
        <p className="text-lg md:text-xl text-[#949ba4] max-w-2xl mb-10">
          {APP_CONFIG.tagline}. Crie servidores organizados por canais de texto e voz, converse com amigos e divirta-se em alta velocidade.
        </p>
        <div className="flex flex-col sm:flex-row gap-4 w-full justify-center">
          <button onClick={() => onNavigate('register')} className="px-8 py-4 rounded-full bg-[#5865f2] text-white font-bold text-lg hover:bg-[#4752c4] transition-colors shadow-lg shadow-[#5865f2]/30 flex items-center justify-center gap-2">
            Criar Conta Gratuitamente <ArrowRight size={20} />
          </button>
          <button onClick={() => onNavigate('login')} className="px-8 py-4 rounded-full border border-current font-bold text-lg hover:bg-zinc-500/10 transition-colors">
            Abrir no Navegador
          </button>
        </div>
      </section>

      <footer className="py-8 text-center text-sm text-[#949ba4] border-t border-zinc-700/20">
        <p>&copy; 2026 {APP_CONFIG.name}. Todos os direitos reservados.</p>
      </footer>
    </div>
  );
}

// ==========================================
// COMPONENTE: TELA DE AUTENTICAÇÃO (LOGIN / REGISTRO)
// ==========================================
function AuthScreen({ mode, onNavigate, onLogin, theme, setTheme }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [username, setUsername] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    onLogin({
      id: 'u_' + Date.now(),
      name: username || email.split('@')[0] || 'Usuário Dcord',
      email: email,
      tag: Math.floor(1000 + Math.random() * 9000).toString(),
      avatar: 'https://images.unsplash.com/photo-1535713875002-d1d0cf377fde?w=150'
    });
  };

  return (
    <div className={`min-h-screen flex items-center justify-center px-4 ${theme === 'dark' ? 'bg-[#313338]' : 'bg-[#5865f2]'}`}>
      <div className={`w-full max-w-md p-8 rounded-2xl shadow-2xl ${theme === 'dark' ? 'bg-[#1e1f22] text-white' : 'bg-white text-black'}`}>
        <div className="text-center mb-8">
          <h2 className="text-2xl font-bold mb-2">{mode === 'login' ? 'Boas-vindas de volta!' : 'Criar uma conta'}</h2>
          <p className="text-sm text-[#949ba4]">{mode === 'login' ? 'Estamos muito animados para ver você novamente!' : 'Junte-se à nossa comunidade hoje.'}</p>
        </div>

        <form onSubmit={handleSubmit} className="space-y-4">
          {mode === 'register' && (
            <div>
              <label className="text-xs font-bold uppercase tracking-wider text-[#949ba4] block mb-1">Nome de Usuário</label>
              <input 
                type="text" 
                required 
                value={username} 
                onChange={(e) => setUsername(e.target.value)} 
                className={`w-full p-3 rounded text-sm outline-none ${theme === 'dark' ? 'bg-[#313338] text-white' : 'bg-zinc-100 text-black'}`}
                placeholder="Seu nome"
              />
            </div>
          )}
          <div>
            <label className="text-xs font-bold uppercase tracking-wider text-[#949ba4] block mb-1">E-mail</label>
            <input 
              type="email" 
              required 
              value={email} 
              onChange={(e) => setEmail(e.target.value)} 
              className={`w-full p-3 rounded text-sm outline-none ${theme === 'dark' ? 'bg-[#313338] text-white' : 'bg-zinc-100 text-black'}`}
              placeholder="seu@email.com"
            />
          </div>
          <div>
            <label className="text-xs font-bold uppercase tracking-wider text-[#949ba4] block mb-1">Senha</label>
            <input 
              type="password" 
              required 
              value={password} 
              onChange={(e) => setPassword(e.target.value)} 
              className={`w-full p-3 rounded text-sm outline-none ${theme === 'dark' ? 'bg-[#313338] text-white' : 'bg-zinc-100 text-black'}`}
              placeholder="••••••••"
            />
          </div>

          <button type="submit" className="w-full py-3 bg-[#5865f2] hover:bg-[#4752c4] text-white rounded font-bold text-sm transition-colors shadow-lg shadow-[#5865f2]/20">
            {mode === 'login' ? 'Entrar' : 'Continuar'}
          </button>
        </form>

        <div className="mt-6 text-center text-sm text-[#949ba4]">
          {mode === 'login' ? (
            <p>Necessita de uma conta? <button onClick={() => onNavigate('register')} className="text-[#5865f2] hover:underline font-bold">Registra-se</button></p>
          ) : (
            <p>Já tem uma conta? <button onClick={() => onNavigate('login')} className="text-[#5865f2] hover:underline font-bold">Entrar</button></p>
          )}
        </div>
      </div>
    </div>
  );
}
