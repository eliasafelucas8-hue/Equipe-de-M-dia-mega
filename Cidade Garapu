import React, { useState, useEffect, useMemo, useRef } from 'react';
import { 
  Users, CalendarDays, ClipboardCheck, BarChart3, Plus, 
  Trash2, Edit, Camera, Clock, UserCheck, AlertCircle, 
  FileText, Upload, Check, X, Save, Info, Gift, History, Star,
  ChevronUp, CheckCircle2, ShieldCheck, Home, UserMinus,
  Presentation, Video, Mic, Image as ImageIcon, Smartphone, BookOpen, Bell,
  Bookmark, CheckSquare, Menu, ListPlus, CalendarPlus, Cloud, CloudOff,
  RefreshCw, Trash, LogOut, LayoutGrid, List, Eye, Lock, Copy, Palette, MapPin, CopyPlus
} from 'lucide-react';

const appId = typeof __app_id !== 'undefined' ? __app_id : 'omega-midia';

// --- Sistema Anti-Crash ---
class ErrorBoundary extends React.Component {
  constructor(props) { super(props); this.state = { hasError: false, error: null }; }
  static getDerivedStateFromError(error) { return { hasError: true, error }; }
  render() {
    if (this.state.hasError) {
      return (
        <div className="min-h-screen bg-red-50 flex flex-col items-center justify-center p-8 text-center">
          <AlertCircle size={64} className="text-red-500 mb-4" />
          <h1 className="text-3xl font-bold text-red-700 mb-2">Erro Interceptado</h1>
          <p className="text-slate-600 mb-6 max-w-md">Uma falha ocorreu na interface, mas seus dados estão seguros localmente.</p>
          <div className="bg-white p-4 rounded border border-red-200 text-xs text-left text-red-500 mb-6 w-full max-w-lg overflow-auto break-words">
            <code>{this.state.error?.toString()}</code>
          </div>
          <button onClick={() => window.location.reload()} className="bg-red-600 hover:bg-red-700 text-white font-bold py-3 px-6 rounded-xl shadow-md">Restaurar Sistema</button>
        </div>
      );
    }
    return this.props.children;
  }
}

// --- Utilitários e Helpers ---
const generateId = () => Math.random().toString(36).substr(2, 9);
const getArray = (val) => Array.isArray(val) ? val : (val ? [val] : []);
const formatDateBR = (dateStr) => {
  if (!dateStr || typeof dateStr !== 'string') return '';
  const parts = dateStr.split('T')[0].split('-');
  return parts.length === 3 ? `${parts[2]}/${parts[1]}/${parts[0]}` : dateStr;
};
const getWeekdayShort = (dateStr) => {
  if (!dateStr || typeof dateStr !== 'string') return '';
  const parts = dateStr.split('-');
  if (parts.length !== 3) return '';
  const date = new Date(parts[0], parts[1] - 1, parts[2]);
  const weekday = date.toLocaleString('pt-BR', { weekday: 'short' }).replace('.', '');
  return weekday.charAt(0).toUpperCase() + weekday.slice(1, 3);
};
const getMonthName = (monthStr) => {
  if (!monthStr || typeof monthStr !== 'string') return '';
  const parts = monthStr.split('-');
  if (parts.length < 2) return '';
  const date = new Date(parts[0], parts[1] - 1, 1);
  const month = date.toLocaleString('pt-BR', { month: 'long' });
  return month.charAt(0).toUpperCase() + month.slice(1);
};
const getTodayLocalStr = () => {
  const d = new Date();
  return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
};
const isDateInNext7Days = (dateStr) => {
  if (!dateStr) return false;
  const today = new Date(getTodayLocalStr() + 'T00:00:00');
  const target = new Date(dateStr + 'T00:00:00');
  const diffDays = Math.round((target - today) / (1000 * 60 * 60 * 24));
  return diffDays >= 0 && diffDays <= 7;
};
const calculateProtocolEndDate = (startStr, durationDays) => {
  if (!startStr || durationDays === undefined || durationDays === null || durationDays === '') return '';
  const parts = startStr.split('-');
  if (parts.length !== 3) return '';
  const date = new Date(parts[0], parts[1] - 1, parts[2]);
  date.setDate(date.getDate() + parseInt(durationDays, 10));
  return `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}-${String(date.getDate()).padStart(2, '0')}`;
};
const calculateDuration = (startStr, endStr) => {
  if (!startStr || !endStr) return 1;
  const s = new Date(startStr);
  const e = new Date(endStr);
  return Math.max(1, Math.ceil((e - s) / (1000 * 60 * 60 * 24)) + 1);
};
const getNthSunday = (year, month, n) => {
  const firstDay = new Date(year, month - 1, 1);
  let dayOfWeek = firstDay.getDay();
  let daysUntilFirstSunday = dayOfWeek === 0 ? 0 : 7 - dayOfWeek;
  let nthSundayDate = 1 + daysUntilFirstSunday + (n - 1) * 7;
  const d = new Date(year, month - 1, nthSundayDate);
  return `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}-${String(d.getDate()).padStart(2, '0')}`;
};
const getEaster = (year) => {
  const a = year % 19, b = Math.floor(year / 100), c = year % 100, d = Math.floor(b / 4), e = b % 4;
  const f = Math.floor((b + 8) / 25), g = Math.floor((b - f + 1) / 3), h = (19 * a + b - d - g + 15) % 30;
  const i = Math.floor(c / 4), k = c % 4, l = (32 + 2 * e + 2 * i - h - k) % 7, m = Math.floor((a + 11 * h + 22 * l) / 451);
  const month = Math.floor((h + l - 7 * m + 114) / 31), day = ((h + l - 7 * m + 114) % 31) + 1;
  return `${year}-${String(month).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
};
const roleOrderMap = { 'Dirigente': 1, 'Vice-Dirigente': 2, 'Secretário(a)': 3, 'Vice-Secretário(a)': 4, 'Secretário': 3, 'Secretária': 3, 'Vice-Secretário': 4, 'Vice-Secretária': 4 };
const sortMembers = (membersList) => {
  if (!Array.isArray(membersList)) return [];
  return [...membersList].sort((a, b) => {
    if (a?.type === 'Diretoria' && b?.type !== 'Diretoria') return -1;
    if (a?.type !== 'Diretoria' && b?.type === 'Diretoria') return 1;
    if (a?.type === 'Diretoria' && b?.type === 'Diretoria') return (roleOrderMap[a.role] || 99) - (roleOrderMap[b.role] || 99);
    return ((a?.usualName || a?.name) || '').localeCompare((b?.usualName || b?.name) || '');
  });
};

const bibleChaptersCount = { 'Gênesis': 50, 'Êxodo': 40, 'Levítico': 27, 'Números': 36, 'Deuteronômio': 34, 'Josué': 24, 'Juízes': 21, 'Rute': 4, '1 Samuel': 31, '2 Samuel': 24, '1 Reis': 22, '2 Reis': 25, '1 Crônicas': 29, '2 Crônicas': 36, 'Esdras': 10, 'Neemias': 13, 'Ester': 10, 'Jó': 42, 'Salmos': 150, 'Provérbios': 31, 'Eclesiastes': 12, 'Cânticos': 8, 'Isaías': 66, 'Jeremias': 52, 'Lamentações': 5, 'Ezequiel': 48, 'Daniel': 12, 'Oséias': 14, 'Joel': 3, 'Amós': 9, 'Obadias': 1, 'Jonas': 4, 'Miquéias': 7, 'Naum': 3, 'Habacuque': 3, 'Sofonias': 3, 'Ageu': 2, 'Zacarias': 14, 'Malaquias': 4, 'Mateus': 28, 'Marcos': 16, 'Lucas': 24, 'João': 21, 'Atos': 28, 'Romanos': 16, '1 Coríntios': 16, '2 Coríntios': 13, 'Gálatas': 6, 'Efésios': 6, 'Filipenses': 4, 'Colossenses': 4, '1 Tessalonicenses': 5, '2 Tessalonicenses': 3, '1 Timóteo': 6, '2 Timóteo': 4, 'Tito': 3, 'Filemom': 1, 'Hebreus': 13, 'Tiago': 5, '1 Pedro': 5, '2 Pedro': 3, '1 João': 5, '2 João': 1, '3 João': 1, 'Judas': 1, 'Apocalipse': 22 };
const bibleBooks = Object.keys(bibleChaptersCount);

const trashDictionary = { name: 'Nome Completo', usualName: 'Nome Usual', dob: 'Data de Nascimento', memberNumber: 'ID/Número do Membro', joinDate: 'Data de Ingresso na Equipe', type: 'Classificação Principal', role: 'Função Exercida', gender: 'Sexo', date: 'Data Registrada', time: 'Horário', eventName: 'Nome do Culto/Evento', category: 'Categoria do Evento', assigned: 'Escalados (IDs)', support: 'Apoio (IDs)', supervisor: 'Vistoria (IDs)', completed: 'Concluída?', title: 'Título / Pauta da Reunião', subjectType: 'Assunto Principal', customTitle: 'Descrição Customizada', subType: 'Aplicação/Subtipo', infraction: 'Motivação/Infração Relatada', startDate: 'Data de Início/Deferimento', endDate: 'Data de Finalização', duration: 'Duração (Dias)', notes: 'Observações do Item', targetProtocolId: 'Processo Alvo (ID)', protocolNumber: 'Número de Registro do Processo', customName: 'Descrição da Data', day: 'Dia do Mês Anual', month: 'Mês Anual', eventType: 'Especificação do Evento', anosCompleting: 'Anos Completando', isJubilee: 'Configurado como Jubileu?', jubileeType: 'Tipo Específico de Jubileu', isConcludedForced: 'Forçado a Conclusão?' };

const eventCodes = { 'Doutrina': '04', 'Santa Ceia': '03', 'Culto de Domingo': '02', 'Culto da Mocidade': '10', 'Culto do PROATI': '58', 'Culto da Família': '02', 'Culto Infantil': '02', 'Consagração': '05', 'Conjunto Musical & Eletrônico': '43', 'Coral': '47', 'União de Adolescentes': '25', 'PROATI': '62', 'Templo': '16', 'Círculo de Oração Infantil': '09', 'Grupo Jovem': '44', 'Círculo de Oração': '15', 'Campanha': '07', 'Escola Bíblica Animada': '36', 'Simpósio': '57', 'Pré-Congresso de Jovens': '29', 'Pré-Congresso de Adolescentes': '29', 'Pré-Congresso Unificado': '29', 'Culto Jovem Unificado': '31', 'Estudo Bíblico': '20', 'Culto para Casais': '18', 'Evangelismo em Massa': '27', 'Evangelismo (Pernambuco para Cristo)': '27', 'Culto de Missões': '17', 'Conferência Missionária': '30', 'Culto Evangelístico': '14', 'Escola Bíblica de Férias': '54', 'Desfile': '01', 'Cruzada Evangelística': '37', 'Seminário': '23', 'COI Missionário': '18', 'Exposição Bíblica': '68', 'Exposição Missionária': '68', 'Vigília': '42', 'Mini-Vigília': '41', 'ProjeFérias': '12', 'Crianças': '50' };

const getEventCode = (name) => {
   if (!name) return null;
   const baseName = name.split(' (')[0]; 
   return eventCodes[baseName] || null;
};

// Listas Ordenadas
const optionsCultos = ['Consagração', 'Culto da Família', 'Culto da Mocidade', 'Culto de Domingo', 'Culto do PROATI', 'Culto Infantil', 'Doutrina', 'Santa Ceia'].sort();
const optionsFest = ['Campanha', 'Círculo de Oração', 'Círculo de Oração Infantil', 'Conjunto Musical & Eletrônico', 'Coral', 'Escola Bíblica Animada', 'Grupo Jovem', 'PROATI', 'Templo', 'União de Adolescentes'].sort();
const optionsEvento = ['COI Missionário', 'Conferência Missionária', 'Cruzada Evangelística', 'Culto de Missões', 'Culto em Ação de Graça', 'Culto Evangelístico', 'Culto Jovem Unificado', 'Culto para Casais', 'Desfile', 'Escola Bíblica de Férias', 'Estudo Bíblico', 'Evangelismo (Pernambuco para Cristo)', 'Evangelismo em Massa', 'Exposição Bíblica', 'Exposição Missionária', 'Mini-Vigília', 'Pré-Congresso de Adolescentes', 'Pré-Congresso de Jovens', 'Pré-Congresso Unificado', 'ProjeFérias', 'Seminário', 'Simpósio', 'Vigília'].sort();
const optionsEncontro = ['Campanhas', 'Comissões', 'Conjuntos', 'Corais', 'Crianças', 'Grupos Jovens', 'Mocidade', 'Uniões'].sort();
const congregacoes = ['Cidade Garapu (Padrão)', 'Garapu II', 'Garapu III', 'Vila Claudete', 'Vila Nova', 'Canaã', 'Vila Nova - Galileia', 'Porto Rico'];
const consagraOrgaos = ['Geral', 'EBD', 'COI', 'COA', 'Coral', 'União', 'Grupo Jovem', 'Mocidade', 'Campanha', 'Conjunto & Eletrônico'];
const projeFeriasTipos = ['Estudo (Or. da Mocidade)', 'Estudo (Domingo)', 'Gincana', 'Estudo (Feriado)', 'Dia Jovem', 'Evangelismo (Mocidade)', 'Encerramento (Premiação)', 'Batalha Bíblica', 'Vestibular Bíblico'];
const jubileeOptions = ['Papel', 'Madeira', 'Estanho', 'Cristal', 'Porcelana', 'Prata', 'Pérola', 'Corais', 'Esmeralda', 'Rubi', 'Ouro', 'Ametista', 'Diamante', 'Safira', 'Vinho'];

// --- Hooks ---
function useBibleVerse(book, chapter, verseStart, verseEnd) {
   const [text, setText] = useState('');
   useEffect(() => {
      if(!book || !chapter || !verseStart) { setText(''); return; }
      let ref = `${book} ${chapter}:${verseStart}`;
      if(verseEnd && parseInt(verseEnd) > parseInt(verseStart)) ref += `-${verseEnd}`;
      // Versão offline não realiza fetch externo.
      setText(`[Modo Offline] Consulta dinâmica ao versículo ${ref} requer integração de banco de dados local da Bíblia.`);
   }, [book, chapter, verseStart, verseEnd]);
   return text;
}

function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      if (item) {
        const parsed = JSON.parse(item);
        if (Array.isArray(initialValue) && !Array.isArray(parsed)) return initialValue;
        if (typeof initialValue === 'object' && typeof parsed !== 'object') return initialValue;
        return parsed;
      }
      return initialValue;
    } catch (error) { return initialValue; }
  });
  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) { console.error("Erro Storage.", error); }
  };
  return [storedValue, setValue];
}

// --- Componentes de UI ---
function SelectionBar({ selectedIds, allIds, onSelectAll, onDeselectAll, onBulkDelete }) {
  if (!selectedIds || selectedIds.length === 0) return null;
  const isAllSelected = selectedIds.length === allIds.length && allIds.length > 0;
  return (
    <div className="bg-blue-50 border border-blue-200 p-3 rounded-xl flex justify-between items-center mb-4 shadow-sm animate-in fade-in sticky top-0 z-20">
       <span className="font-bold text-blue-800 text-sm">{selectedIds.length} item(s) selecionado(s)</span>
       <div className="flex gap-2">
          <button onClick={isAllSelected ? onDeselectAll : onSelectAll} className="px-3 py-1.5 bg-white border border-blue-200 rounded-lg font-bold text-xs text-blue-600 shadow-sm transition-colors hover:bg-blue-100">{isAllSelected ? 'Desmarcar Todos' : 'Selecionar Todos'}</button>
          <button onClick={onBulkDelete} className="px-3 py-1.5 bg-red-500 hover:bg-red-600 text-white rounded-lg font-bold text-xs flex items-center gap-1 shadow-sm transition-colors"><Trash2 size={14}/> Excluir</button>
       </div>
    </div>
  );
}

function PinModal({ isOpen, onClose, onSuccess }) {
   const [pin, setPin] = useState('');
   const [error, setError] = useState(false);
   if (!isOpen) return null;
   const handleSubmit = (e) => {
      e.preventDefault();
      if (pin === '1992') { setError(false); setPin(''); onSuccess(); onClose(); } 
      else { setError(true); setPin(''); }
   };
   return (
      <div className="fixed inset-0 bg-slate-900/80 backdrop-blur-sm flex items-center justify-center p-4 z-[200]">
         <form onSubmit={handleSubmit} className="bg-white rounded-2xl shadow-2xl w-full max-w-xs p-6 flex flex-col items-center animate-in zoom-in-95">
            <div className="bg-slate-100 p-4 rounded-full mb-4"><Lock size={32} className="text-slate-600"/></div>
            <h3 className="font-bold text-xl mb-2 text-slate-800">Acesso Restrito</h3>
            <p className="text-sm text-slate-500 text-center mb-6">Insira o PIN de 4 dígitos para autorizar.</p>
            <input type="password" maxLength="4" autoFocus required value={pin} onChange={e=>setPin(e.target.value.replace(/\D/g, ''))} className={`w-full text-center text-2xl tracking-[0.5em] font-bold p-3 border-2 rounded-xl mb-2 outline-none transition-colors ${error ? 'border-red-500 bg-red-50 text-red-700' : 'border-slate-300 focus:border-blue-500'}`} placeholder="••••" />
            {error && <p className="text-red-500 text-xs font-bold mb-4 animate-bounce">PIN Incorreto!</p>}
            <div className="flex gap-3 w-full mt-4">
               <button type="button" onClick={onClose} className="flex-1 py-2.5 bg-slate-100 text-slate-600 font-bold rounded-lg hover:bg-slate-200 transition-colors">Cancelar</button>
               <button type="submit" disabled={pin.length !== 4} className="flex-1 py-2.5 bg-slate-900 text-white font-bold rounded-lg hover:bg-slate-800 transition-colors disabled:opacity-50">Autorizar</button>
            </div>
         </form>
      </div>
   );
}

function ImageCropper({ src, onCrop, onCancel }) {
  const canvasRef = useRef(null);
  const [scale, setScale] = useState(1);
  const [offset, setOffset] = useState({ x: 0, y: 0 });
  const [isDragging, setIsDragging] = useState(false);
  const [dragStart, setDragStart] = useState({ x: 0, y: 0 });
  const imgRef = useRef(null);

  useEffect(() => { const img = new Image(); img.src = src; img.onload = () => { imgRef.current = img; draw(); }; }, [src, scale, offset]);

  const draw = () => {
    const canvas = canvasRef.current; if (!canvas || !imgRef.current) return;
    const ctx = canvas.getContext('2d');
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#f8fafc'; ctx.fillRect(0, 0, canvas.width, canvas.height);
    ctx.save(); ctx.translate(canvas.width / 2, canvas.height / 2);
    const img = imgRef.current;
    const baseScale = Math.max(canvas.width / img.width, canvas.height / img.height);
    const finalScale = baseScale * scale;
    ctx.scale(finalScale, finalScale);
    ctx.drawImage(img, -img.width / 2 + offset.x / finalScale, -img.height / 2 + offset.y / finalScale);
    ctx.restore();
    ctx.fillStyle = 'rgba(15, 23, 42, 0.7)'; ctx.beginPath();
    ctx.rect(0, 0, canvas.width, canvas.height);
    ctx.arc(canvas.width / 2, canvas.height / 2, canvas.width / 2, 0, Math.PI * 2, true); ctx.fill();
  };

  const handleMouseDown = (e) => { setIsDragging(true); setDragStart({ x: e.clientX - offset.x, y: e.clientY - offset.y }); };
  const handleMouseMove = (e) => { if (!isDragging) return; setOffset({ x: e.clientX - dragStart.x, y: e.clientY - dragStart.y }); };
  const handleMouseUp = () => setIsDragging(false);
  
  const handleCrop = () => {
    const canvas = document.createElement('canvas'); canvas.width = 250; canvas.height = 250;
    const ctx = canvas.getContext('2d'); const img = imgRef.current;
    const baseScale = Math.max(canvas.width / img.width, canvas.height / img.height);
    const finalScale = baseScale * scale;
    ctx.fillStyle = '#fff'; ctx.fillRect(0, 0, canvas.width, canvas.height);
    ctx.translate(canvas.width / 2, canvas.height / 2); ctx.scale(finalScale, finalScale);
    ctx.drawImage(img, -img.width / 2 + offset.x / finalScale, -img.height / 2 + offset.y / finalScale);
    onCrop(canvas.toDataURL('image/jpeg', 0.85));
  };

  return (
    <div className="fixed inset-0 bg-slate-900/90 backdrop-blur-sm z-[110] flex items-center justify-center p-4">
      <div className="bg-white p-6 rounded-2xl shadow-xl w-full max-w-sm flex flex-col items-center">
        <h3 className="font-bold text-xl text-slate-800 mb-2">Ajustar Foto</h3>
        <canvas ref={canvasRef} width={250} height={250} onMouseDown={handleMouseDown} onMouseMove={handleMouseMove} onMouseUp={handleMouseUp} onMouseLeave={handleMouseUp} onTouchStart={(e) => handleMouseDown({clientX: e.touches[0].clientX, clientY: e.touches[0].clientY})} onTouchMove={(e) => handleMouseMove({clientX: e.touches[0].clientX, clientY: e.touches[0].clientY})} onTouchEnd={handleMouseUp} className="cursor-move rounded-full shadow-inner border border-slate-200 bg-slate-50" />
        <div className="w-full mt-6 space-y-2"><label className="text-sm font-semibold flex justify-between"><span>Zoom</span><span className="text-blue-600">{Math.round(scale * 100)}%</span></label><input type="range" min="1" max="3" step="0.05" value={scale} onChange={e => setScale(parseFloat(e.target.value))} className="w-full accent-blue-600" /></div>
        <div className="flex gap-3 w-full mt-8"><button type="button" onClick={onCancel} className="flex-1 py-2.5 bg-slate-100 rounded-lg font-bold">Cancelar</button><button type="button" onClick={handleCrop} className="flex-1 py-2.5 bg-blue-600 text-white rounded-lg font-bold">Salvar</button></div>
      </div>
    </div>
  );
}

export default function App() { return <ErrorBoundary><MainApp /></ErrorBoundary>; }

// --- MAIN APPLICATION ---
function MainApp() {
  const [activeTab, setActiveTab] = useState('inicio');
  const [isSidebarCollapsed, setIsSidebarCollapsed] = useState(true);
  const [showTrash, setShowTrash] = useState(false);
  const [trashDetails, setTrashDetails] = useState(null);
  const [selectedNotification, setSelectedNotification] = useState(null);
  const [pinModal, setPinModal] = useState({ isOpen: false, action: null });
  const [viewMode, setViewMode] = useState('grid');

  const [rawMembers, setMembers] = useLocalStorage('omega_members', []);
  const [rawSchedules, setSchedules] = useLocalStorage('omega_schedules', []);
  const [rawAttendances, setAttendances] = useLocalStorage('omega_attendances', {});
  const [rawProtocols, setProtocols] = useLocalStorage('omega_protocols', []);
  const [rawFestivities, setFestivities] = useLocalStorage('omega_festivities', []);
  const [rawRatings, setRatings] = useLocalStorage('omega_ratings', {});
  const [rawMeetings, setMeetings] = useLocalStorage('omega_meetings', []);
  const [rawMeetingAttendances, setMeetingAttendances] = useLocalStorage('omega_meeting_attendances', {});
  const [rawPostings, setPostings] = useLocalStorage('omega_postings', {});
  const [rawCustomDates, setCustomDates] = useLocalStorage('omega_custom_dates', []);
  const [rawTrash, setTrash] = useLocalStorage('omega_trash', []);

  const members = getArray(rawMembers);
  const schedules = getArray(rawSchedules);
  const protocols = getArray(rawProtocols);
  const festivities = getArray(rawFestivities);
  const meetings = getArray(rawMeetings);
  const customDates = getArray(rawCustomDates);
  const attendances = rawAttendances || {};
  const ratings = rawRatings || {};
  const meetingAttendances = rawMeetingAttendances || {};
  const postings = rawPostings || {};
  const trashItems = getArray(rawTrash);

  const getTrashItemName = (t) => {
    if (t.type === 'Membro') return t.data.usualName || t.data.name;
    if (t.type === 'Escala') return t.data.eventName;
    if (t.type === 'Festividade') return `${t.data.category}: ${t.data.eventType}`;
    if (t.type === 'Reunião') return t.data.title;
    if (t.type === 'Protocolo') return `Nº. ${t.data.protocolNumber || 'S/N'} - ${t.data.subType}`;
    if (t.type === 'Data') return t.data.customName || `${t.data.type} (${t.data.role})`;
    return 'Item Excluído';
  };

  useEffect(() => {
    const now = new Date();
    const validTrash = trashItems.filter(t => (now - new Date(t.deletedAt)) < 30 * 24 * 60 * 60 * 1000);
    if (validTrash.length !== trashItems.length) setTrash(validTrash);
  // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  const moveToTrash = (type, data) => {
    setTrash([...trashItems, { id: generateId(), type, data, deletedAt: new Date().toISOString() }]);
  };

  const exMemberIds = useMemo(() => {
    return protocols.filter(p => p.subType === 'Exclusão' || p.subType === 'Solicitação de Desligamento').map(p => p.memberId);
  }, [protocols]);

  const unavailableMemberIds = useMemo(() => {
    const todayStr = getTodayLocalStr();
    return protocols.filter(p => {
      if (['Suspensão de Nível 1', 'Suspensão de Nível 2', 'Afastamento Disciplinar', 'Solicitação de Afastamento'].includes(p.subType)) {
        if (p.isConcludedForced) return false;
        if (p.startDate && p.startDate <= todayStr && (!p.endDate || p.endDate > todayStr)) return true;
      }
      return false;
    }).map(p => p.memberId);
  }, [protocols]);

  const [showNotifications, setShowNotifications] = useState(false);
  const notifications = useMemo(() => {
    const alerts = [];
    const currentYear = new Date().getFullYear();

    members.forEach(m => {
      if (m?.dob && !exMemberIds.includes(m.id)) {
        const dParts = m.dob.split('-');
        if (dParts.length === 3) {
           const bdayThisYear = `${currentYear}-${dParts[1]}-${dParts[2]}`;
           if (isDateInNext7Days(bdayThisYear)) {
             alerts.push({ id: `bday-${m.id}-${currentYear}`, type: 'birthday', icon: <Gift size={16} className="text-pink-500"/>, title: `Aniversário de Integrante`, text: `O(A) integrante ${m.usualName || m.name} completará ano no dia ${dParts[2]}/${dParts[1]}. Envie suas felicitações!` });
           }
        }
      }
    });

    const stdDates = [
      { id: 'mulher', name: 'Dia Internacional da Mulher', date: `${currentYear}-03-08` },
      { id: 'pascoa', name: 'Páscoa', date: getEaster(currentYear) },
      { id: 'maes', name: 'Dia das Mães', date: getNthSunday(currentYear, 5, 2) },
      { id: 'pastor', name: 'Dia do Pastor', date: getNthSunday(currentYear, 6, 2) },
      { id: 'pais', name: 'Dia dos Pais', date: getNthSunday(currentYear, 8, 2) },
      { id: 'missoes', name: 'Dia Nacional de Missões', date: getNthSunday(currentYear, 9, 2) },
      { id: 'ebd', name: 'Dia da Escola Dominical', date: getNthSunday(currentYear, 9, 3) },
      { id: 'biblia', name: 'Dia Nacional da Bíblia', date: getNthSunday(currentYear, 12, 2) },
      { id: 'natal', name: 'Natal', date: `${currentYear}-12-25` }
    ];
    
    const allImpDates = [...stdDates, ...getArray(customDates).map(c => ({ ...c, name: c.type === 'Outros' ? c.customName : `${c.type} (${c.role})`, date: `${currentYear}-${c.month}-${c.day}` }))];
    allImpDates.forEach(d => {
       if (d.date && isDateInNext7Days(d.date)) {
          alerts.push({ id: `date-${d.id}-${currentYear}`, type: 'important_date', icon: <Bookmark size={16} className="text-emerald-500"/>, title: `Data Importante se Aproximando`, text: `A data comemorativa "${d.name}" acontecerá em ${formatDateBR(d.date)}.` });
       }
    });

    meetings.forEach(m => {
      if (m?.date && !m.completed && isDateInNext7Days(m.date)) {
        alerts.push({ id: `meet-${m.id}`, type: 'meeting', icon: <Presentation size={16} className="text-indigo-500"/>, title: `Reunião Agendada`, text: `A reunião de pauta "${m.title}" está agendada para ${formatDateBR(m.date)} às ${m.time}. Não falte!` });
      }
    });

    festivities.forEach(f => {
      getArray(f.agendamentos).forEach(a => {
         if (a.date && isDateInNext7Days(a.date)) {
            alerts.push({ id: `agend-${a.id}`, type: 'agendamento', icon: <Video size={16} className="text-purple-500"/>, title: `Agendamento de Mídia`, text: `A atividade "${a.tipo}" referente ao evento "${f.eventType}" está agendada para ${formatDateBR(a.date)} às ${a.time}.` });
         }
      });
    });

    return alerts;
  }, [members, meetings, festivities, customDates, exMemberIds]);

  const [dialog, setDialog] = useState({ isOpen: false, type: 'confirm', message: '', onConfirm: null });
  const confirmAction = (message, onConfirm) => setDialog({ isOpen: true, type: 'confirm', message, onConfirm });
  const alertAction = (message) => setDialog({ isOpen: true, type: 'alert', message, onConfirm: null });

  return (
    <div className="min-h-screen bg-slate-50 text-slate-800 font-sans flex flex-col relative overflow-hidden w-full">
      {!isSidebarCollapsed && (
        <div className="fixed inset-0 bg-slate-900/50 z-40 backdrop-blur-sm transition-opacity" onClick={() => setIsSidebarCollapsed(true)}></div>
      )}
      
      <nav className={`fixed top-0 left-0 h-full z-50 w-64 bg-slate-900 text-white flex flex-col shadow-2xl transform transition-transform duration-300 ${isSidebarCollapsed ? '-translate-x-full' : 'translate-x-0'}`}>
        <div className="bg-slate-950 flex flex-col items-center py-6 px-4 border-b border-slate-800 relative">
          <button onClick={() => setIsSidebarCollapsed(true)} className="absolute top-4 right-4 text-slate-400 hover:text-white"><X size={20}/></button>
          <div className="bg-white rounded-full flex items-center justify-center shadow-lg shadow-blue-900/50 overflow-hidden border-2 border-slate-700 relative w-20 h-20 mb-3">
            <img src="/logo.png" alt="Logo" className="w-full h-full object-cover z-10" onError={(e) => { e.target.style.display='none'; e.target.nextSibling.style.display='flex'; }} />
            <div className="absolute inset-0 hidden items-center justify-center bg-blue-600 text-white font-bold text-[10px] text-center p-1">Logo</div>
          </div>
          <div className="flex flex-col items-center mt-2">
            <h1 className="text-md font-bold text-center tracking-wide leading-tight whitespace-nowrap">EQUIPE DE MÍDIA<br/>ÔMEGA</h1>
            <p className="text-xs italic text-slate-400 mt-1 whitespace-nowrap font-medium">IEADPE Cidade Garapu</p>
          </div>
        </div>
        
        <div className="flex-1 p-3 space-y-2 overflow-y-auto scrollbar-hide flex flex-col">
          <NavButton icon={<Home size={20} />} label="Página Inicial" isActive={activeTab === 'inicio'} onClick={() => {setActiveTab('inicio'); setIsSidebarCollapsed(true);}} />
          <NavButton icon={<Users size={20} />} label="Membros" isActive={activeTab === 'membros'} onClick={() => {setActiveTab('membros'); setIsSidebarCollapsed(true);}} />
          <NavButton icon={<Gift size={20} />} label="Aniversariantes" isActive={activeTab === 'aniversariantes'} onClick={() => {setActiveTab('aniversariantes'); setIsSidebarCollapsed(true);}} />
          <NavButton icon={<Bookmark size={20} />} label="Datas Importantes" isActive={activeTab === 'datas_importantes'} onClick={() => {setActiveTab('datas_importantes'); setIsSidebarCollapsed(true);}} />
          <NavButton icon={<CalendarDays size={20} />} label="Escalas" isActive={activeTab === 'escalas'} onClick={() => {setActiveTab('escalas'); setIsSidebarCollapsed(true);}} />
          <NavButton icon={<Star size={20} />} label="Festividades & Eventos" isActive={activeTab === 'festividades'} onClick={() => {setActiveTab('festividades'); setIsSidebarCollapsed(true);}} />
          <NavButton icon={<Presentation size={20} />} label="Reuniões" isActive={activeTab === 'reunioes'} onClick={() => {setActiveTab('reunioes'); setIsSidebarCollapsed(true);}} />
          <NavButton icon={<FileText size={20} />} label="Protocolos" isActive={activeTab === 'protocolos'} onClick={() => {setActiveTab('protocolos'); setIsSidebarCollapsed(true);}} />
          <div className="mt-auto pt-4 border-t border-slate-800 space-y-2">
            <NavButton icon={<History size={20} />} label="Histórico" isActive={activeTab === 'historico'} onClick={() => {setActiveTab('historico'); setIsSidebarCollapsed(true);}} />
          </div>
        </div>
      </nav>

      <main className="flex-1 w-full min-h-screen overflow-hidden flex flex-col relative pl-0">
        <header className="bg-white border-b border-slate-200 px-4 py-3 flex justify-between items-center shadow-sm shrink-0 z-30">
           <div className="flex items-center gap-3">
             <button onClick={() => setIsSidebarCollapsed(!isSidebarCollapsed)} className="p-2 bg-slate-100 text-slate-600 hover:bg-slate-200 rounded-lg transition-colors"><Menu size={20} /></button>
             <div className="hidden sm:flex items-center gap-2 text-emerald-600 bg-emerald-50 px-3 py-1 rounded-full border border-emerald-100"><CheckCircle2 size={16} /><span className="text-xs font-bold">Salvo Local</span></div>
             
             {/* Visualização Toggle */}
             <div className="hidden md:flex items-center bg-slate-100 p-1 rounded-lg ml-4">
                <button onClick={() => setViewMode('grid')} className={`p-1.5 rounded flex items-center justify-center transition-colors ${viewMode === 'grid' ? 'bg-white shadow-sm text-blue-600' : 'text-slate-500 hover:text-slate-700'}`} title="Visualização em Grade"><LayoutGrid size={16} /></button>
                <button onClick={() => setViewMode('list')} className={`p-1.5 rounded flex items-center justify-center transition-colors ${viewMode === 'list' ? 'bg-white shadow-sm text-blue-600' : 'text-slate-500 hover:text-slate-700'}`} title="Visualização em Lista"><List size={16} /></button>
             </div>
           </div>
           
           <div className="flex items-center gap-2 md:gap-4 relative">
             <button onClick={() => setShowTrash(true)} className="p-2 rounded-full hover:bg-red-50 hover:text-red-500 transition-colors text-slate-500" title="Lixeira (Itens Removidos)">
               <Trash size={20} />
             </button>

             <button onClick={() => setShowNotifications(!showNotifications)} className="p-2 rounded-full hover:bg-slate-100 transition-colors relative text-slate-600 border border-transparent">
               <Bell size={20} />
               {notifications.length > 0 && (<span className="absolute top-0.5 right-0.5 w-4 h-4 bg-red-500 text-white text-[10px] font-bold rounded-full flex items-center justify-center border-2 border-white">{notifications.length}</span>)}
             </button>
             
             {showNotifications && (
               <div className="absolute right-0 top-12 w-80 bg-white border border-slate-200 shadow-2xl rounded-xl overflow-hidden z-50 animate-in fade-in slide-in-from-top-2">
                 <div className="bg-slate-900 text-white p-3 font-bold text-sm">Lembretes Semanais (7 Dias)</div>
                 {('Notification' in window && Notification.permission !== 'granted' && Notification.permission !== 'denied') && (
                   <div className="bg-blue-50 p-4 border-b border-blue-100 flex flex-col items-center text-center gap-3">
                      <p className="text-xs text-blue-800 font-medium">Ative os balões interativos no aparelho!</p>
                      <button onClick={() => Notification.requestPermission().then(p => { if(p === 'granted') alertAction('Notificações ativadas!'); })} className="text-xs bg-blue-600 text-white px-4 py-2 rounded-lg font-bold shadow-md hover:bg-blue-700 w-full">Ativar Notificações</button>
                   </div>
                 )}
                 <div className="max-h-[60vh] overflow-y-auto">
                   {notifications.length === 0 ? (<div className="p-4 text-center text-sm text-slate-500">Sem alertas para os próximos 7 dias.</div>) : (
                     <ul className="divide-y divide-slate-100">
                       {notifications.map(n => (
                         <li key={n.id} className="p-3 flex items-start gap-3 hover:bg-slate-50 cursor-pointer transition-colors" onClick={() => setSelectedNotification(n)}>
                           <div className="mt-0.5 shrink-0 bg-slate-100 p-1.5 rounded-lg">{n.icon}</div>
                           <div className="flex-1 min-w-0">
                              <h4 className="font-bold text-slate-800 text-sm leading-tight truncate">{n.title}</h4>
                              <p className="text-xs text-slate-500 truncate mt-0.5">{n.text}</p>
                           </div>
                         </li>
                       ))}
                     </ul>
                   )}
                 </div>
               </div>
             )}
           </div>
        </header>

        <div className="flex-1 overflow-y-auto p-4 md:p-8 w-full max-w-7xl mx-auto scrollbar-hide">
          {activeTab === 'inicio' && <HomeView members={members} exMemberIds={exMemberIds} schedules={schedules} attendances={attendances} ratings={ratings} setRatings={setRatings} viewMode={viewMode} customDates={customDates} meetings={meetings} festivities={festivities}/>}
          {activeTab === 'membros' && <MembersView members={members} setMembers={setMembers} exMemberIds={exMemberIds} schedules={schedules} attendances={attendances} protocols={protocols} confirmAction={confirmAction} moveToTrash={moveToTrash} viewMode={viewMode}/>}
          {activeTab === 'aniversariantes' && <BirthdaysView members={members} exMemberIds={exMemberIds} viewMode={viewMode}/>}
          {activeTab === 'datas_importantes' && <ImportantDatesView customDates={customDates} setCustomDates={setCustomDates} confirmAction={confirmAction} moveToTrash={moveToTrash} viewMode={viewMode}/>}
          {activeTab === 'escalas' && <SchedulesView members={members} exMemberIds={exMemberIds} unavailableMemberIds={unavailableMemberIds} schedules={schedules} setSchedules={setSchedules} attendances={attendances} setAttendances={setAttendances} postings={postings} setPostings={setPostings} confirmAction={confirmAction} moveToTrash={moveToTrash} viewMode={viewMode} festivities={festivities}/>}
          {activeTab === 'festividades' && <FestivitiesView festivities={festivities} setFestivities={setFestivities} schedules={schedules} setSchedules={setSchedules} confirmAction={confirmAction} moveToTrash={moveToTrash} viewMode={viewMode}/>}
          {activeTab === 'reunioes' && <MeetingsView members={members} exMemberIds={exMemberIds} meetings={meetings} setMeetings={setMeetings} meetingAttendances={meetingAttendances} setMeetingAttendances={setMeetingAttendances} confirmAction={confirmAction} moveToTrash={moveToTrash} viewMode={viewMode} setPinModal={setPinModal}/>}
          {activeTab === 'protocolos' && <ProtocolsView members={members} exMemberIds={exMemberIds} protocols={protocols} setProtocols={setProtocols} confirmAction={confirmAction} moveToTrash={moveToTrash} viewMode={viewMode} setPinModal={setPinModal}/>}
          {activeTab === 'historico' && <HistoryView members={members} exMemberIds={exMemberIds} schedules={schedules} setSchedules={setSchedules} attendances={attendances} postings={postings} protocols={protocols} confirmAction={confirmAction} moveToTrash={moveToTrash} viewMode={viewMode}/>}
        </div>
      </main>

      <PinModal isOpen={pinModal.isOpen} onClose={()=>setPinModal({isOpen:false, action:null})} onSuccess={()=>{if(pinModal.action) pinModal.action();}} />

      {showTrash && (
        <div className="fixed inset-0 bg-slate-900/80 backdrop-blur-sm flex items-center justify-center p-4 z-[100]">
           <div className="bg-white rounded-2xl w-full max-w-3xl flex flex-col max-h-[90vh] shadow-2xl">
              <div className="p-4 border-b flex justify-between items-center bg-slate-50 rounded-t-2xl">
                 <h2 className="font-bold text-lg flex items-center gap-2 text-slate-800"><Trash className="text-red-500"/> Lixeira (30 dias)</h2>
                 <div className="flex gap-2">
                    <button onClick={()=>confirmAction('Esvaziar toda a lixeira? Essa ação é irreversível.', ()=>setTrash([]))} className="px-3 py-1.5 bg-red-100 hover:bg-red-200 transition-colors text-red-700 text-sm font-bold rounded-lg shadow-sm">Esvaziar</button>
                    <button onClick={()=>setShowTrash(false)} className="p-1.5 hover:bg-slate-200 rounded-lg transition-colors"><X/></button>
                 </div>
              </div>
              <div className="p-4 overflow-y-auto flex-1 space-y-3">
                 {trashItems.length === 0 ? <p className="text-center text-slate-500 py-8">A lixeira está vazia.</p> : trashItems.map(t => (
                   <div key={t.id} className="p-4 border border-slate-200 rounded-xl flex flex-col sm:flex-row justify-between sm:items-center text-sm hover:bg-slate-50 transition-colors gap-4 shadow-sm">
                      <div className="flex-1 break-words min-w-0 flex flex-col">
                         <div className="mb-1.5"><span className="font-bold text-slate-700 uppercase text-[10px] tracking-wider bg-slate-200 px-2 py-0.5 rounded shadow-sm">{t.type}</span></div>
                         <span className="font-bold text-slate-800 whitespace-normal block text-base">{getTrashItemName(t)}</span>
                         <div className="text-xs text-slate-500 mt-1 font-medium">Excluído em: <span className="font-bold text-slate-600">{formatDateBR(t.deletedAt.split('T')[0])}</span></div>
                      </div>
                      <div className="flex flex-wrap gap-2 shrink-0 mt-2 sm:mt-0">
                         <button onClick={()=>{setTrashDetails(t)}} className="px-3 py-1.5 bg-blue-50 text-blue-600 font-bold rounded-lg text-xs flex items-center justify-center gap-1 transition-colors hover:bg-blue-100 shadow-sm"><Eye size={14}/> Detalhes</button>
                         <button onClick={()=>{
                            confirmAction('Restaurar item original?', () => {
                               if(t.type === 'Membro') setMembers([...members, t.data]);
                               if(t.type === 'Escala') setSchedules([...schedules, t.data]);
                               if(t.type === 'Festividade') setFestivities([...festivities, t.data]);
                               if(t.type === 'Reunião') setMeetings([...meetings, t.data]);
                               if(t.type === 'Protocolo') setProtocols([...protocols, t.data]);
                               if(t.type === 'Data') setCustomDates([...customDates, t.data]);
                               setTrash(trashItems.filter(x=>x.id!==t.id));
                            });
                         }} className="px-3 py-1.5 bg-emerald-100 text-emerald-700 font-bold rounded-lg text-xs flex items-center justify-center gap-1 transition-colors hover:bg-emerald-200 shadow-sm"><RefreshCw size={14}/> Restaurar</button>
                         <button onClick={()=>confirmAction('Excluir permanentemente?', ()=>setTrash(trashItems.filter(x=>x.id!==t.id)))} className="px-3 py-1.5 bg-red-50 text-red-500 font-bold rounded-lg text-xs flex items-center justify-center gap-1 transition-colors hover:bg-red-100 shadow-sm"><Trash2 size={14}/> Excluir</button>
                      </div>
                   </div>
                 ))}
              </div>
           </div>
        </div>
      )}
      
      {/* Detalhes da Lixeira Formatados */}
      {trashDetails && (
         <div className="fixed inset-0 bg-slate-900/60 flex items-center justify-center p-4 z-[120] backdrop-blur-sm">
            <div className="bg-white p-6 rounded-xl w-full max-w-lg relative shadow-2xl max-h-[80vh] flex flex-col">
               <div className="flex justify-between items-center border-b pb-3 mb-4 shrink-0">
                  <h3 className="font-bold text-lg text-slate-800 flex items-center gap-2"><Info size={18}/> Dossiê do Item Excluído</h3>
                  <button onClick={()=>setTrashDetails(null)} className="p-1.5 hover:bg-slate-100 rounded-lg text-slate-500 transition-colors"><X size={20}/></button>
               </div>
               <div className="overflow-y-auto flex-1 text-sm bg-slate-50 p-4 rounded-lg border border-slate-200 space-y-3">
                  <div className="mb-2"><span className="text-[10px] uppercase font-bold text-slate-400 tracking-wider">Tipo de Dado:</span> <div className="font-bold text-blue-700 text-base">{trashDetails.type}</div></div>
                  {Object.entries(trashDetails.data).map(([key, val]) => {
                     if(key === 'id' || key === 'photoUrl') return null; 
                     let displayVal = val;
                     if(Array.isArray(val)) displayVal = val.length > 0 ? val.join(', ') : 'Nenhum';
                     else if(typeof val === 'boolean') displayVal = val ? 'Sim' : 'Não';
                     else if(typeof val === 'object') displayVal = 'Objeto Complexo';
                     if(!displayVal) displayVal = '-';

                     if(typeof displayVal === 'string' && displayVal.match(/^\d{4}-\d{2}-\d{2}$/)) {
                        displayVal = formatDateBR(displayVal);
                     }
                     
                     // Helper Especial para os Nomes de Membros e Protocolos na Lixeira
                     if (key === 'memberId' && (trashDetails.type === 'Protocolo' || trashDetails.type === 'Ex-Membro')) {
                        const m = members.find(x=>x.id===val);
                        displayVal = m ? (m.usualName || m.name) : 'Membro Não Encontrado';
                     }
                     if (key === 'targetProtocolId' && trashDetails.type === 'Protocolo') {
                        const p = protocols.find(x=>x.id===val);
                        displayVal = p ? `Nº. ${p.protocolNumber}` : 'Nenhum / Não Informado';
                     }

                     const translatedKey = trashDictionary[key] || key;

                     return (
                        <div key={key} className="border-t border-slate-100 pt-2 break-words">
                           <span className="text-[10px] uppercase font-bold text-slate-400 tracking-wider block mb-0.5">{translatedKey}</span>
                           <span className="font-medium text-slate-700 whitespace-normal">{displayVal}</span>
                        </div>
                     )
                  })}
               </div>
            </div>
         </div>
      )}

      {/* Detalhes da Notificação */}
      {selectedNotification && (
         <div className="fixed inset-0 bg-slate-900/60 flex items-center justify-center p-4 z-[120] backdrop-blur-sm">
            <div className="bg-white p-6 rounded-xl w-full max-w-sm relative shadow-2xl flex flex-col">
               <div className="flex justify-between items-center border-b pb-3 mb-4 shrink-0">
                  <h3 className="font-bold text-lg text-slate-800 flex items-center gap-2">{selectedNotification.icon} Detalhes do Alerta</h3>
                  <button onClick={()=>setSelectedNotification(null)} className="p-1 hover:bg-slate-100 rounded-lg text-slate-500 transition-colors"><X size={20}/></button>
               </div>
               <div className="text-center p-4">
                  <div className="bg-blue-50 p-4 rounded-xl mb-4 text-blue-600 flex justify-center w-16 mx-auto">{selectedNotification.icon}</div>
                  <h4 className="font-bold text-xl text-slate-800 mb-2">{selectedNotification.title}</h4>
                  <p className="text-slate-600 whitespace-normal leading-relaxed">{selectedNotification.text}</p>
               </div>
               <button onClick={()=>setSelectedNotification(null)} className="mt-4 w-full py-2 bg-slate-900 text-white font-bold rounded-lg hover:bg-slate-800 transition-colors">Fechar</button>
            </div>
         </div>
      )}

      {/* Modal Confirm Compartilhado */}
      {dialog.isOpen && (
        <div className="fixed inset-0 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center p-4 z-[130]">
          <div className="bg-white rounded-2xl shadow-2xl w-full max-w-sm p-6 animate-in fade-in zoom-in-95 duration-200">
            <div className="flex flex-col items-center text-center">
              {dialog.type === 'confirm' ? <AlertCircle size={48} className="text-blue-500 mb-4" /> : <Info size={48} className="text-emerald-500 mb-4" />}
              <p className="text-slate-800 font-medium mb-6 break-words whitespace-normal">{dialog.message}</p>
            </div>
            <div className="flex gap-3 justify-center">
              {dialog.type === 'confirm' && (<button onClick={() => setDialog({ ...dialog, isOpen: false })} className="px-4 py-2 bg-slate-100 text-slate-600 hover:bg-slate-200 rounded-lg font-bold w-full transition-colors shadow-sm">Cancelar</button>)}
              <button onClick={() => { if (dialog.onConfirm) dialog.onConfirm(); setDialog({ ...dialog, isOpen: false }); }} className="px-4 py-2 bg-blue-600 text-white hover:bg-blue-700 rounded-lg font-bold w-full shadow-md transition-colors">Sim</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}

function NavButton({ icon, label, isActive, onClick }) {
  return (
    <button onClick={onClick} className={`flex items-center gap-3 w-full p-3 rounded-lg transition-all duration-200 shrink-0 border border-transparent ${isActive ? 'bg-blue-600 text-white shadow-md' : 'text-slate-300 hover:bg-slate-800 hover:text-white'} justify-start`} title={label}>
      <div className="w-5 h-5 flex items-center justify-center shrink-0">{icon}</div>
      <span className="font-medium text-sm whitespace-nowrap overflow-hidden">{label}</span>
    </button>
  );
}

function CalendarWidget({ festivities, meetings, customDates, members, exMemberIds }) {
   const [currDate, setCurrDate] = useState(new Date());
   const [selectedDayEvents, setSelectedDayEvents] = useState(null);

   const year = currDate.getFullYear();
   const month = currDate.getMonth();
   const daysInMonth = new Date(year, month + 1, 0).getDate();
   const firstDayIdx = new Date(year, month, 1).getDay();

   const eventsMap = useMemo(() => {
      const map = {};
      const addEvent = (dStr, evt) => { if(!map[dStr]) map[dStr] = []; map[dStr].push(evt); };

      festivities.forEach(f => {
         if(f.startDate) addEvent(f.startDate, { type: 'festividade', data: f, color: 'bg-amber-500' });
         getArray(f.agendamentos).forEach(a => {
            if(a.date && a.date.startsWith(`${year}-${String(month+1).padStart(2,'0')}`)) addEvent(a.date, { type: 'agendamento', data: a, festData: f, color: 'bg-purple-500' });
         });
      });

      meetings.filter(m => !m.completed && m.date && m.date.startsWith(`${year}-${String(month+1).padStart(2,'0')}`)).forEach(m => {
         addEvent(m.date, { type: 'reuniao', data: m, color: 'bg-indigo-500' });
      });

      const stdDates = [
         { name: 'Dia Int. da Mulher', date: `${year}-03-08` }, { name: 'Páscoa', date: getEaster(year) },
         { name: 'Dia das Mães', date: getNthSunday(year, 5, 2) }, { name: 'Dia do Pastor', date: getNthSunday(year, 6, 2) },
         { name: 'Dia dos Pais', date: getNthSunday(year, 8, 2) }, { name: 'Dia Nac. de Missões', date: getNthSunday(year, 9, 2) },
         { name: 'Dia da EBD', date: getNthSunday(year, 9, 3) }, { name: 'Dia Nac. da Bíblia', date: getNthSunday(year, 12, 2) },
         { name: 'Natal', date: `${year}-12-25` }
      ];
      [...stdDates, ...getArray(customDates).map(c=>({ name: c.type==='Outros'?c.customName:`${c.type} (${c.role})`, date: `${year}-${c.month}-${c.day}`}))].forEach(d => {
         if(d.date && d.date.startsWith(`${year}-${String(month+1).padStart(2,'0')}`)) addEvent(d.date, { type: 'data_importante', data: d, color: 'bg-emerald-500' });
      });

      const activeMembers = members.filter(m => !exMemberIds.includes(m.id));
      activeMembers.forEach(m => {
         if(m.dob) {
            const [, mMonth, mDay] = m.dob.split('-');
            if (parseInt(mMonth,10) === month + 1) addEvent(`${year}-${mMonth}-${mDay}`, { type: 'aniversario', data: m, color: 'bg-pink-500' });
         }
      });

      return map;
   }, [festivities, meetings, customDates, members, exMemberIds, year, month]);

   const blanks = Array.from({length: firstDayIdx}, (_, i) => i);
   const days = Array.from({length: daysInMonth}, (_, i) => i + 1);
   const monthNames = ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'];

   return (
      <div className="bg-white rounded-2xl shadow-sm border p-4 md:p-6 w-full">
         <div className="flex justify-between items-center mb-6">
            <h3 className="text-xl font-bold text-slate-800 flex items-center gap-2"><CalendarDays className="text-blue-600"/> Calendário Geral</h3>
            <div className="flex items-center gap-4 bg-slate-100 p-1.5 rounded-lg">
               <button onClick={()=>setCurrDate(new Date(year, month - 1, 1))} className="p-1 hover:bg-white rounded transition-colors">&lt;</button>
               <span className="font-bold text-sm min-w-[120px] text-center text-slate-700 uppercase tracking-wider">{monthNames[month]} {year}</span>
               <button onClick={()=>setCurrDate(new Date(year, month + 1, 1))} className="p-1 hover:bg-white rounded transition-colors">&gt;</button>
            </div>
         </div>
         <div className="grid grid-cols-7 gap-1 md:gap-2 text-center text-xs font-bold text-slate-400 mb-2 uppercase tracking-wider"><div>Dom</div><div>Seg</div><div>Ter</div><div>Qua</div><div>Qui</div><div>Sex</div><div>Sáb</div></div>
         <div className="grid grid-cols-7 gap-1 md:gap-2">
            {blanks.map(b => <div key={`blank-${b}`} className="p-2 md:p-4 rounded-xl bg-slate-50/50 border border-transparent"></div>)}
            {days.map(d => {
               const dStr = `${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
               const dayEvts = eventsMap[dStr] || [];
               const isToday = dStr === getTodayLocalStr();
               return (
                  <div key={d} onClick={()=>{if(dayEvts.length>0) setSelectedDayEvents({date: dStr, events: dayEvts})}} className={`p-1 md:p-2 border rounded-xl flex flex-col items-center justify-start min-h-[60px] md:min-h-[80px] transition-colors relative cursor-pointer hover:border-blue-300 hover:shadow-sm ${isToday ? 'bg-blue-50 border-blue-200' : 'bg-white border-slate-200'}`}>
                     <span className={`text-sm md:text-base font-bold ${isToday ? 'text-blue-700' : 'text-slate-700'}`}>{d}</span>
                     <div className="flex flex-wrap justify-center gap-1 mt-auto w-full px-1">
                        {dayEvts.slice(0, 4).map((evt, i) => <div key={i} className={`w-2 h-2 rounded-full ${evt.color}`}></div>)}
                        {dayEvts.length > 4 && <span className="text-[10px] font-bold text-slate-500 leading-none">+{dayEvts.length-4}</span>}
                     </div>
                  </div>
               )
            })}
         </div>

         <div className="mt-6 flex flex-wrap gap-4 text-[10px] font-bold uppercase tracking-wider justify-center bg-slate-50 p-3 rounded-lg border">
            <div className="flex items-center gap-1.5"><div className="w-3 h-3 rounded-full bg-amber-500"></div> Festividades</div>
            <div className="flex items-center gap-1.5"><div className="w-3 h-3 rounded-full bg-indigo-500"></div> Reuniões</div>
            <div className="flex items-center gap-1.5"><div className="w-3 h-3 rounded-full bg-emerald-500"></div> Datas Imp.</div>
            <div className="flex items-center gap-1.5"><div className="w-3 h-3 rounded-full bg-pink-500"></div> Aniversários</div>
            <div className="flex items-center gap-1.5"><div className="w-3 h-3 rounded-full bg-purple-500"></div> Agendamentos</div>
         </div>

         {selectedDayEvents && (
            <div className="fixed inset-0 bg-slate-900/60 flex justify-center items-center p-4 z-[100] backdrop-blur-sm">
               <div className="bg-white rounded-2xl p-6 w-full max-w-md shadow-2xl relative max-h-[80vh] flex flex-col">
                  <button onClick={()=>setSelectedDayEvents(null)} className="absolute top-4 right-4 hover:bg-slate-100 rounded-lg p-1.5 transition-colors text-slate-500"><X/></button>
                  <h3 className="font-bold text-xl mb-4 border-b pb-2 flex items-center gap-2"><CalendarDays className="text-blue-500"/> {formatDateBR(selectedDayEvents.date)}</h3>
                  <div className="overflow-y-auto space-y-3 pr-2 flex-1">
                     {selectedDayEvents.events.map((evt, i) => (
                        <div key={i} className="p-3 border rounded-xl bg-slate-50 flex items-start gap-3 shadow-sm">
                           <div className={`mt-1 w-3 h-3 rounded-full shrink-0 ${evt.color}`}></div>
                           <div className="flex-1 min-w-0">
                              {evt.type === 'festividade' && <><span className="text-[10px] uppercase font-bold text-slate-500 block">Início de Evento</span><strong className="text-slate-800 leading-tight block">{evt.data.category}: {evt.data.eventType}</strong></>}
                              {evt.type === 'reuniao' && <><span className="text-[10px] uppercase font-bold text-slate-500 block">Reunião</span><strong className="text-slate-800 leading-tight block">{evt.data.title}</strong><span className="text-xs text-slate-600"><Clock size={12} className="inline mr-1 -mt-0.5"/>{evt.data.time} | Formato: {evt.data.format}</span></>}
                              {evt.type === 'data_importante' && <><span className="text-[10px] uppercase font-bold text-slate-500 block">Data Importante</span><strong className="text-slate-800 leading-tight block">{evt.data.name}</strong></>}
                              {evt.type === 'aniversario' && <><span className="text-[10px] uppercase font-bold text-slate-500 block">Aniversário de Vida</span><strong className="text-slate-800 leading-tight block">{evt.data.usualName || evt.data.name}</strong></>}
                              {evt.type === 'agendamento' && <><span className="text-[10px] uppercase font-bold text-slate-500 block">Agendamento de Mídia ({evt.festData.eventType})</span><strong className="text-slate-800 leading-tight block">{evt.data.tipo}</strong><span className="text-xs text-slate-600"><Clock size={12} className="inline mr-1 -mt-0.5"/>{evt.data.time}</span></>}
                           </div>
                        </div>
                     ))}
                  </div>
               </div>
            </div>
         )}
      </div>
   );
}

function HomeView({ members, exMemberIds, schedules, attendances, ratings, setRatings, viewMode, customDates, meetings, festivities }) {
  const activeMembers = sortMembers(members.filter(m => !exMemberIds.includes(m.id)));
  const diretoria = activeMembers.filter(m => m.type === 'Diretoria');
  const [filterType, setFilterType] = useState('Todos'); 
  const [filterMonth, setFilterMonth] = useState(''); 
  const [filterYear, setFilterYear] = useState(new Date().getFullYear().toString());
  const [filterQuarter, setFilterQuarter] = useState('1'); 

  const filteredSchedules = useMemo(() => {
    return schedules.filter(s => {
      if (filterType === 'Todos') return true;
      if (!s.date) return false;
      const parts = s.date.split('-');
      const sYear = parts[0], sMonthStr = parts[1];
      if (filterType === 'Mensal') return filterMonth === `${sYear}-${sMonthStr}`;
      if (filterType === 'Trimestral') return filterYear === sYear && filterQuarter === (Math.floor((parseInt(sMonthStr, 10) - 1) / 3) + 1).toString();
      if (filterType === 'Anual') return filterYear === sYear;
      return true;
    });
  }, [schedules, filterType, filterMonth, filterYear, filterQuarter]);

  const generateStats = useMemo(() => {
    return activeMembers.map(member => {
      let totalSchedules = 0, present = 0, absent = 0, justified = 0, onTime = 0, totalRating = 0;
      filteredSchedules.forEach(schedule => {
        const isInvolved = getArray(schedule.assigned).includes(member.id) || getArray(schedule.support).includes(member.id) || getArray(schedule.supervisor).includes(member.id);
        if (isInvolved) {
          totalSchedules++;
          const record = attendances[schedule.id]?.[member.id];
          if (record) {
            if (record.status === 'Presente') { present++; if (record.arrivalTime && record.arrivalTime <= schedule.time) onTime++; if (record.rating) totalRating += record.rating; }
            else if (record.status === 'Ausente') absent++; else if (record.status === 'Justificado') justified++;
          }
        }
      });
      const validSchedules = present + absent;
      const assiduity = validSchedules > 0 ? Math.round((present / validSchedules) * 100) : (totalSchedules === 0 ? null : 100);
      const punctuality = present > 0 ? Math.round((onTime / present) * 100) : (present === 0 ? null : 100);
      const performance = present > 0 ? parseFloat((totalRating / present).toFixed(1)) : 0;
      const performancePct = present > 0 ? Math.round((performance / 5) * 100) : null;
      return { ...member, stats: { totalSchedules, present, absent, justified, onTime, assiduity, punctuality, performance, performancePct } };
    });
  }, [activeMembers, filteredSchedules, attendances]);

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-8 animate-in fade-in duration-300">
      <div className="bg-gradient-to-r from-slate-900 to-blue-900 rounded-2xl p-6 md:p-8 text-white shadow-lg relative overflow-hidden">
        <h2 className="text-2xl md:text-3xl font-bold mb-2">Equipe de Mídia Ômega</h2>
        <p className="text-blue-200 mb-8 max-w-xl text-sm md:text-base">Gerencie membros, escalas e desempenho com proteção local de dados.</p>
        <div className="grid grid-cols-1 sm:grid-cols-3 gap-4">
          <div className="bg-white/10 backdrop-blur-sm border border-white/20 p-4 rounded-xl flex items-center gap-4"><div className="bg-blue-500/30 p-3 rounded-lg"><Users size={24} className="text-blue-100" /></div><div className="min-w-0"><p className="text-blue-100 text-sm font-medium truncate">Ativos</p><p className="text-2xl font-bold">{activeMembers.length}</p></div></div>
          <div className="bg-white/10 backdrop-blur-sm border border-white/20 p-4 rounded-xl flex items-center gap-4"><div className="bg-amber-500/30 p-3 rounded-lg"><CalendarDays size={24} className="text-amber-100" /></div><div className="min-w-0"><p className="text-amber-100 text-sm font-medium truncate">Vigentes</p><p className="text-2xl font-bold">{schedules.filter(s => !s.completed).length}</p></div></div>
          <div className="bg-white/10 backdrop-blur-sm border border-white/20 p-4 rounded-xl flex items-center gap-4"><div className="bg-emerald-500/30 p-3 rounded-lg"><CheckCircle2 size={24} className="text-emerald-100" /></div><div className="min-w-0"><p className="text-emerald-100 text-sm font-medium truncate">Concluídas</p><p className="text-2xl font-bold">{schedules.filter(s => s.completed).length}</p></div></div>
        </div>
      </div>
      <CalendarWidget festivities={festivities} meetings={meetings} customDates={customDates} members={members} exMemberIds={exMemberIds}/>
      <div>
        <h3 className="text-xl font-bold text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-200 pb-2"><ShieldCheck className="text-amber-500"/> Composição da Diretoria (Hierarquia)</h3>
        {diretoria.length === 0 ? <p className="text-slate-500 text-sm bg-slate-50 p-4 rounded-xl border">Não há membros na diretoria.</p> : (
          <div className={viewMode === 'grid' ? "grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4" : "flex flex-col gap-3"}>
            {diretoria.map(member => (
              <div key={member.id} className="bg-white p-4 rounded-xl border shadow-sm flex items-center gap-3 hover:shadow-md transition-shadow">
                {member.photoUrl ? <img src={member.photoUrl} className="w-12 h-12 rounded-full object-cover border-2 border-amber-200 shrink-0" /> : <div className="w-12 h-12 rounded-full bg-amber-50 text-amber-600 flex items-center justify-center font-bold uppercase border-2 border-amber-200 shrink-0">{member.name.substring(0,2)}</div>}
                <div className="overflow-hidden min-w-0 flex-1 break-words"><h4 className="font-bold text-slate-800 text-sm whitespace-normal leading-tight">{member.usualName || member.name}</h4><p className="text-xs font-semibold text-amber-600 break-words">{member.role}</p></div>
              </div>
            ))}
          </div>
        )}
      </div>
      <div className="pt-6">
        <div className="flex flex-col md:flex-row justify-between md:items-end gap-4 border-b pb-4 mb-6">
          <div><h3 className="text-2xl font-bold text-slate-800 flex items-center gap-2"><BarChart3 className="text-blue-600"/> Desempenho</h3></div>
          <div className="bg-white p-3 rounded-lg border shadow-sm flex flex-col md:flex-row gap-3 items-end">
            <div><select className="p-2 border rounded-lg text-sm bg-slate-50 font-bold text-slate-700" value={filterType} onChange={e => setFilterType(e.target.value)}><option value="Todos">Todo Histórico</option><option value="Mensal">Mensal</option><option value="Trimestral">Trimestral</option><option value="Anual">Anual</option></select></div>
            {filterType === 'Mensal' && (<div><input type="month" className="p-2 border rounded-lg text-sm bg-slate-50 font-bold text-slate-700" value={filterMonth} onChange={e => setFilterMonth(e.target.value)} /></div>)}
            {filterType === 'Trimestral' && (<><select className="p-2 border rounded-lg text-sm w-24 bg-slate-50 font-bold text-slate-700" value={filterQuarter} onChange={e => setFilterQuarter(e.target.value)}><option value="1">1º Tri</option><option value="2">2º Tri</option><option value="3">3º Tri</option><option value="4">4º Tri</option></select><input type="number" className="p-2 border rounded-lg text-sm w-20 bg-slate-50 font-bold text-slate-700" value={filterYear} onChange={e => setFilterYear(e.target.value)} /></>)}
            {filterType === 'Anual' && (<div><input type="number" className="p-2 border rounded-lg text-sm w-24 bg-slate-50 font-bold text-slate-700" value={filterYear} onChange={e => setFilterYear(e.target.value)} /></div>)}
          </div>
        </div>
        <div className={gridClass}>
          {generateStats.map(member => (
            <div key={member.id} className="bg-white rounded-xl shadow-sm border overflow-hidden flex flex-col hover:shadow-md transition-shadow">
              <div className="p-4 border-b flex items-center gap-3">
                 {member.photoUrl ? <img src={member.photoUrl} className={`w-12 h-12 rounded-full object-cover border-2 shrink-0 ${member.type === 'Diretoria' ? 'border-amber-200' : 'border-blue-200'}`} /> : <div className={`w-12 h-12 rounded-full flex items-center justify-center font-bold border-2 shrink-0 ${member.type === 'Diretoria' ? 'bg-amber-50 text-amber-600 border-amber-200' : 'bg-blue-50 text-blue-600 border-blue-200'}`}>{member.name.substring(0,2)}</div>}
                 <div className="flex-1 min-w-0 break-words"><h3 className="font-bold text-sm whitespace-normal leading-tight flex items-start gap-1">{member.type === 'Diretoria' && <ShieldCheck size={14} className="text-amber-500 shrink-0 mt-0.5"/>}{member.usualName || member.name}</h3><p className="text-xs text-slate-500 break-words font-medium">{member.type} • {member.role}</p></div>
              </div>
              <div className="p-4 space-y-4 bg-slate-50 flex-1">
                  <div className="grid grid-cols-3 gap-2 text-center text-xs mb-4">
                     <div className="bg-emerald-100 p-2 rounded text-emerald-800"><div className="font-bold text-lg">{member.stats.present}</div>Presenças</div>
                     <div className="bg-red-100 p-2 rounded text-red-800"><div className="font-bold text-lg">{member.stats.absent}</div>Ausências</div>
                     <div className="bg-amber-100 p-2 rounded text-amber-800"><div className="font-bold text-lg">{member.stats.justified}</div>Justificadas</div>
                  </div>
                  <div className="space-y-4">
                    <div><div className="flex justify-between text-xs mb-1 font-bold"><span>Assiduidade</span><span>{member.stats.assiduity === null ? '-' : `${member.stats.assiduity}%`}</span></div><div className="w-full bg-slate-200 rounded-full h-2"><div className="h-2 rounded-full bg-emerald-500" style={{width:`${member.stats.assiduity||0}%`}}></div></div></div>
                    <div><div className="flex justify-between text-xs mb-1 font-bold"><span>Pontualidade</span><span>{member.stats.punctuality === null ? '-' : `${member.stats.punctuality}%`}</span></div><div className="w-full bg-slate-200 rounded-full h-2"><div className="h-2 rounded-full bg-blue-500" style={{width:`${member.stats.punctuality||0}%`}}></div></div></div>
                    <div><div className="flex justify-between text-xs mb-1 font-bold"><span>Desempenho</span><span>{member.stats.performancePct === null ? '-' : `${member.stats.performancePct}% (${member.stats.performance})`}</span></div><div className="w-full bg-slate-200 rounded-full h-2"><div className="h-2 rounded-full bg-amber-500" style={{width:`${member.stats.performancePct||0}%`}}></div></div></div>
                  </div>
              </div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

function MembersView({ members, setMembers, exMemberIds, schedules, attendances, protocols, confirmAction, moveToTrash, viewMode }) {
  const [showForm, setShowForm] = useState(false);
  const [selectedMemberInfo, setSelectedMemberInfo] = useState(null);
  const [cropImageSrc, setCropImageSrc] = useState(null);
  const [inputFirstName, setInputFirstName] = useState('');
  const [availableSurnames, setAvailableSurnames] = useState([]);
  const [formData, setFormData] = useState({ id: '', photoUrl: '', name: '', usualName: '', dob: '', gender: 'Masculino', memberNumber: '', type: 'Efetivo', role: 'Fotógrafo', joinDate: '', dobDay: '01', dobMonth: '01', dobYear: '2000', joinDay: '01', joinMonth: '01', joinYear: '2024' });
  const fileInputRef = useRef(null);
  const [selectedIds, setSelectedIds] = useState([]);

  const daysOpts = Array.from({length: 31}, (_, i) => String(i + 1).padStart(2, '0'));
  const monthOpts = Array.from({length: 12}, (_, i) => String(i + 1).padStart(2, '0'));
  const currYear = new Date().getFullYear();
  const dobYearOpts = Array.from({length: 100}, (_, i) => String(currYear - i));
  const joinYearOpts = Array.from({length: 50}, (_, i) => String(currYear - i));

  const getRolesByGender = (type, gender) => {
     if (type === 'Efetivo') return gender === 'Masculino' ? ['Fotógrafo', 'Videomaker', 'Entrevistador', 'Roteirista', 'Designer Gráfico'] : ['Fotógrafa', 'Videomaker', 'Entrevistadora', 'Roteirista', 'Designer Gráfico'];
     else return gender === 'Masculino' ? ['Dirigente', 'Vice-Dirigente', 'Secretário', 'Vice-Secretário'] : ['Dirigente', 'Vice-Dirigente', 'Secretária', 'Vice-Secretária'];
  };
  
  const activeMembers = sortMembers(members.filter(m => !exMemberIds.includes(m.id)));
  const allIds = activeMembers.map(m => m.id);
  const diretoria = activeMembers.filter(m => m.type === 'Diretoria');
  const efetivos = activeMembers.filter(m => m.type === 'Efetivo');

  const handleNameChange = (e) => {
     const fullName = e.target.value;
     const parts = fullName.split(' ').filter(p => p.trim() && !['e', 'da', 'de', 'do', 'das', 'dos'].includes(p.toLowerCase()));
     if (parts.length > 0) {
        setInputFirstName(parts[0]);
        setAvailableSurnames(parts.slice(1));
        const usualParts = formData.usualName ? formData.usualName.split(' ') : [];
        if (usualParts.length < 2) setFormData(prev => ({...prev, name: fullName, usualName: `${parts[0]} ${parts[1] || ''}`.trim()}));
        else setFormData(prev => ({...prev, name: fullName}));
     } else {
        setInputFirstName('');
        setAvailableSurnames([]);
        setFormData(prev => ({...prev, name: fullName}));
     }
  };

  const handleGenderOrTypeChange = (field, value) => {
     setFormData(prev => {
        const next = { ...prev, [field]: value };
        const newRoles = getRolesByGender(next.type, next.gender);
        if (!newRoles.includes(next.role)) next.role = newRoles[0];
        return next;
     });
  };

  const openFormForNew = () => {
     const defaultJoin = getTodayLocalStr().split('-');
     setFormData({ id: '', photoUrl: '', name: '', usualName: '', dob: '', gender: 'Masculino', memberNumber: '', type: 'Efetivo', role: 'Fotógrafo', joinDate: '', dobDay: '01', dobMonth: '01', dobYear: '2000', joinDay: defaultJoin[2], joinMonth: defaultJoin[1], joinYear: defaultJoin[0] });
     setInputFirstName('');
     setAvailableSurnames([]);
     setShowForm(true);
  };

  const openFormForEdit = (m) => {
     const dobParts = m.dob ? m.dob.split('-') : ['2000', '01', '01'];
     const joinParts = m.joinDate ? m.joinDate.split('-') : getTodayLocalStr().split('-');
     const parts = m.name.split(' ').filter(p => p.trim() && !['e', 'da', 'de', 'do', 'das', 'dos'].includes(p.toLowerCase()));
     setInputFirstName(parts[0] || '');
     setAvailableSurnames(parts.slice(1));
     setFormData({...m, dobDay: dobParts[2], dobMonth: dobParts[1], dobYear: dobParts[0], joinDay: joinParts[2], joinMonth: joinParts[1], joinYear: joinParts[0]});
     setShowForm(true);
  }

  const handleSubmit = (e) => { 
    e.preventDefault(); 
    confirmAction('Deseja salvar as informações deste membro?', () => {
      const finalDob = `${formData.dobYear}-${formData.dobMonth}-${formData.dobDay}`;
      const finalJoin = `${formData.joinYear}-${formData.joinMonth}-${formData.joinDay}`;
      const usualParts = formData.usualName ? formData.usualName.split(' ') : [];
      const finalUsualName = `${usualParts[0] || inputFirstName} ${usualParts[1] || ''}`.trim();
      const dataToSave = { ...formData, dob: finalDob, joinDate: finalJoin, usualName: finalUsualName };
      if (formData.id) setMembers(members.map(m => m.id === formData.id ? dataToSave : m)); 
      else setMembers([...members, { ...dataToSave, id: generateId() }]); 
      setShowForm(false); 
    });
  };
  
  const deleteMember = (m) => { confirmAction('Remover para a lixeira?', () => { moveToTrash('Membro', m); setMembers(members.filter(x => x.id !== m.id)); setSelectedIds(prev => prev.filter(id => id !== m.id)); }); };

  const handleBulkDelete = () => {
     selectedIds.forEach(id => {
        const m = members.find(x => x.id === id);
        if(m) moveToTrash('Membro', m);
     });
     setMembers(members.filter(m => !selectedIds.includes(m.id)));
     setSelectedIds([]);
  };

  const getDetailedStats = (mem) => {
    let totalSchedules = 0, present = 0, absent = 0, justified = 0, onTime = 0, totalRating = 0;
    schedules.forEach(schedule => {
      const isInvolved = getArray(schedule.assigned).includes(mem.id) || getArray(schedule.support).includes(mem.id) || getArray(schedule.supervisor).includes(mem.id);
      if (isInvolved) {
        totalSchedules++;
        const record = attendances[schedule.id]?.[mem.id];
        if (record) {
          if (record.status === 'Presente') { present++; if (record.arrivalTime && record.arrivalTime <= schedule.time) onTime++; if (record.rating) totalRating += record.rating; }
          else if (record.status === 'Ausente') absent++; else if (record.status === 'Justificado') justified++;
        }
      }
    });
    const valid = present + absent;
    const assiduity = valid > 0 ? Math.round((present/valid)*100) : 100;
    const punctuality = present > 0 ? Math.round((onTime/present)*100) : 100;
    const performance = present > 0 ? parseFloat((totalRating/present).toFixed(1)) : 0;
    const memberProtocols = protocols.filter(p => p.memberId === mem.id);
    return { totalSchedules, present, absent, justified, assiduity, punctuality, performance, memberProtocols };
  };

  const toggleSelection = (id) => { setSelectedIds(prev => prev.includes(id) ? prev.filter(x => x !== id) : [...prev, id]); };

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 md:grid-cols-3 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-6 relative animate-in fade-in duration-300">
      {cropImageSrc && <ImageCropper src={cropImageSrc} onCrop={(c) => { setFormData({...formData, photoUrl: c}); setCropImageSrc(null); }} onCancel={() => setCropImageSrc(null)} />}
      <div className="flex justify-between items-center border-b pb-4">
        <h2 className="text-2xl font-bold">Integrantes Ativos</h2>
        {!showForm && <button onClick={openFormForNew} className="bg-blue-600 text-white px-4 py-2 rounded-lg flex items-center gap-2 font-bold shadow-md hover:bg-blue-700 transition-colors"><Plus size={20} /> Novo</button>}
      </div>

      <SelectionBar selectedIds={selectedIds} allIds={allIds} onSelectAll={()=>setSelectedIds(allIds)} onDeselectAll={()=>setSelectedIds([])} onBulkDelete={handleBulkDelete} />

      {showForm && (
        <form onSubmit={handleSubmit} className="bg-white p-6 rounded-xl border shadow-sm grid grid-cols-1 md:grid-cols-2 gap-4">
          <div className="md:col-span-2"><label className="block text-sm mb-1 font-bold">Nome Completo</label><input required className="w-full p-2 border rounded-lg bg-slate-50 font-bold text-slate-700" value={formData.name} onChange={handleNameChange} /></div>
          <div className="md:col-span-2 border p-4 rounded-xl bg-slate-50">
             <label className="block text-sm mb-2 font-bold text-slate-800">Nome Usual (Exibição nas Escalas e Lembretes)</label>
             <div className="flex gap-2 items-center">
                <input required className="flex-1 p-2 border rounded-lg bg-white font-bold text-slate-700" value={formData.usualName ? formData.usualName.split(' ')[0] : inputFirstName} onChange={e => {
                   const secondPart = formData.usualName ? formData.usualName.split(' ')[1] : '';
                   setFormData({...formData, usualName: `${e.target.value} ${secondPart||''}`.trim()});
                }} placeholder="1º Nome" />
                <select className="flex-1 p-2 border rounded-lg bg-white font-bold text-slate-700" value={formData.usualName ? formData.usualName.split(' ')[1] : ''} onChange={e => {
                   const firstPart = formData.usualName ? formData.usualName.split(' ')[0] : inputFirstName;
                   setFormData({...formData, usualName: `${firstPart} ${e.target.value}`.trim()});
                }}>
                   <option value="">Segundo Nome (Opcional)</option>
                   {availableSurnames.map(sn => <option key={sn} value={sn}>{sn}</option>)}
                </select>
             </div>
          </div>
          <div><label className="block text-sm mb-1 font-bold">Sexo</label><select className="w-full p-2 border rounded-lg bg-slate-50 font-bold text-slate-700" value={formData.gender} onChange={e => handleGenderOrTypeChange('gender', e.target.value)}><option value="Masculino">Masculino</option><option value="Feminino">Feminino</option></select></div>
          <div><label className="block text-sm mb-1 font-bold">Número Membro</label><input required className="w-full p-2 border rounded-lg bg-slate-50 font-bold text-slate-700" value={formData.memberNumber} onChange={e => setFormData({...formData, memberNumber: e.target.value})} /></div>
          <div>
             <label className="block text-sm mb-1 font-bold">Data Nasc.</label>
             <div className="flex gap-2">
                <select className="p-2 border rounded-lg font-bold bg-slate-50 text-slate-700 w-1/3" value={formData.dobDay} onChange={e=>setFormData({...formData, dobDay: e.target.value})}>{daysOpts.map(d=><option key={d}>{d}</option>)}</select>
                <select className="p-2 border rounded-lg font-bold bg-slate-50 text-slate-700 w-1/3" value={formData.dobMonth} onChange={e=>setFormData({...formData, dobMonth: e.target.value})}>{monthOpts.map(m=><option key={m}>{m}</option>)}</select>
                <select className="p-2 border rounded-lg font-bold bg-slate-50 text-slate-700 w-1/3" value={formData.dobYear} onChange={e=>setFormData({...formData, dobYear: e.target.value})}>{dobYearOpts.map(y=><option key={y}>{y}</option>)}</select>
             </div>
          </div>
          <div>
             <label className="block text-sm mb-1 font-bold">Ingresso</label>
             <div className="flex gap-2">
                <select className="p-2 border rounded-lg font-bold bg-slate-50 text-slate-700 w-1/3" value={formData.joinDay} onChange={e=>setFormData({...formData, joinDay: e.target.value})}>{daysOpts.map(d=><option key={d}>{d}</option>)}</select>
                <select className="p-2 border rounded-lg font-bold bg-slate-50 text-slate-700 w-1/3" value={formData.joinMonth} onChange={e=>setFormData({...formData, joinMonth: e.target.value})}>{monthOpts.map(m=><option key={m}>{m}</option>)}</select>
                <select className="p-2 border rounded-lg font-bold bg-slate-50 text-slate-700 w-1/3" value={formData.joinYear} onChange={e=>setFormData({...formData, joinYear: e.target.value})}>{joinYearOpts.map(y=><option key={y}>{y}</option>)}</select>
             </div>
          </div>
          <div><label className="block text-sm mb-1 font-bold">Tipo</label><select className="w-full p-2 border rounded-lg bg-slate-50 font-bold text-slate-700" value={formData.type} onChange={e => handleGenderOrTypeChange('type', e.target.value)}><option value="Efetivo">Efetivo</option><option value="Diretoria">Diretoria</option></select></div>
          <div><label className="block text-sm mb-1 font-bold">Função</label><select className="w-full p-2 border rounded-lg bg-slate-50 font-bold text-slate-700" value={formData.role} onChange={e => setFormData({...formData, role: e.target.value})}>{getRolesByGender(formData.type, formData.gender).map(r => <option key={r}>{r}</option>)}</select></div>
          <div className="md:col-span-2"><label className="block text-sm mb-1 font-bold">Foto</label><div className="flex gap-4 items-center">{formData.photoUrl && <img src={formData.photoUrl} className="w-16 h-16 rounded-full object-cover shadow-sm" />}<button type="button" onClick={() => fileInputRef.current.click()} className="px-4 py-2 bg-slate-100 rounded-lg font-bold text-slate-700 hover:bg-slate-200 transition-colors shadow-sm">Upload Foto</button><input type="file" ref={fileInputRef} className="hidden" accept="image/*" onChange={(e) => { if(e.target.files[0]) { const r = new FileReader(); r.onload = ev => setCropImageSrc(ev.target.result); r.readAsDataURL(e.target.files[0]); } e.target.value=null; }} /></div></div>
          <div className="md:col-span-2 text-right flex justify-end gap-2 mt-4 border-t pt-4">
             <button type="button" onClick={() => setShowForm(false)} className="px-6 py-2 border rounded-lg text-slate-600 font-bold hover:bg-slate-50 transition-colors">Cancelar</button>
             <button type="submit" className="bg-slate-900 text-white px-6 py-2 rounded-lg font-bold shadow-md hover:bg-slate-800 transition-colors">Salvar</button>
          </div>
        </form>
      )}

      {diretoria.length > 0 && <div><h3 className="text-xl font-bold mb-4 border-b pb-2 flex items-center gap-2 text-slate-800"><ShieldCheck className="text-amber-500"/> Diretoria</h3><div className={gridClass}>{diretoria.map(m => <MemberCard key={m.id} m={m} selected={selectedIds.includes(m.id)} onToggle={()=>toggleSelection(m.id)} onEdit={()=>openFormForEdit(m)} onDelete={()=>deleteMember(m)} onInfo={()=>setSelectedMemberInfo(m)} />)}</div></div>}
      {efetivos.length > 0 && <div className="pt-4"><h3 className="text-xl font-bold mb-4 border-b pb-2 flex items-center gap-2 text-slate-800"><Users className="text-blue-500"/> Efetivos</h3><div className={gridClass}>{efetivos.map(m => <MemberCard key={m.id} m={m} selected={selectedIds.includes(m.id)} onToggle={()=>toggleSelection(m.id)} onEdit={()=>openFormForEdit(m)} onDelete={()=>deleteMember(m)} onInfo={()=>setSelectedMemberInfo(m)} />)}</div></div>}

      {selectedMemberInfo && (
        <div className="fixed inset-0 bg-slate-900/60 flex justify-center items-center p-4 z-[90] backdrop-blur-sm">
           <div className="bg-white rounded-2xl w-full max-w-3xl p-6 md:p-8 relative max-h-[90vh] overflow-y-auto shadow-2xl flex flex-col">
              <button onClick={() => setSelectedMemberInfo(null)} className="absolute top-4 right-4 hover:bg-slate-100 rounded-lg p-1.5 transition-colors text-slate-500"><X/></button>
              <div className="flex flex-col md:flex-row gap-6 items-center md:items-start mb-8 border-b pb-6">
                 {selectedMemberInfo.photoUrl ? <img src={selectedMemberInfo.photoUrl} className="w-32 h-32 rounded-full object-cover border-4 border-slate-200 shrink-0 shadow-sm"/> : <div className="w-32 h-32 rounded-full bg-slate-200 flex items-center justify-center font-bold text-4xl border-4 border-slate-300 shrink-0 text-slate-500">{selectedMemberInfo.name.substring(0,2)}</div>}
                 <div className="min-w-0 flex-1 text-center md:text-left space-y-2">
                    <h2 className="text-2xl md:text-3xl font-bold whitespace-normal leading-tight text-slate-800">{selectedMemberInfo.name}</h2>
                    <p className="text-blue-600 font-black uppercase tracking-wider whitespace-normal">{selectedMemberInfo.role}</p>
                    <div className="flex flex-wrap gap-4 justify-center md:justify-start mt-2">
                       <div className="bg-slate-50 border p-2 rounded-lg"><span className="block text-[10px] uppercase font-bold text-slate-400">Ingresso</span><span className="font-bold text-slate-700">{formatDateBR(selectedMemberInfo.joinDate)}</span></div>
                       <div className="bg-slate-50 border p-2 rounded-lg"><span className="block text-[10px] uppercase font-bold text-slate-400">Nascimento</span><span className="font-bold text-slate-700">{formatDateBR(selectedMemberInfo.dob)}</span></div>
                       <div className="bg-slate-50 border p-2 rounded-lg"><span className="block text-[10px] uppercase font-bold text-slate-400">ID Membro</span><span className="font-bold text-slate-700">{selectedMemberInfo.memberNumber}</span></div>
                    </div>
                 </div>
                 <div className="shrink-0 bg-white p-2 border border-slate-200 rounded-xl shadow-sm flex flex-col items-center">
                    <div className="w-24 h-24 mb-1 bg-slate-100 flex flex-col items-center justify-center rounded-lg border border-slate-300 text-slate-400">
                       <Smartphone size={32} />
                       <span className="text-[8px] mt-1 text-center font-bold">QR OFFLINE</span>
                    </div>
                    <span className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Dossiê</span>
                 </div>
              </div>
              
              {(() => {
                 const stats = getDetailedStats(selectedMemberInfo);
                 return (
                   <div className="space-y-8">
                      <div>
                         <h3 className="font-bold border-b pb-2 mb-4 text-slate-800 text-lg flex items-center gap-2"><BarChart3 className="text-blue-500"/> Resumo de Desempenho</h3>
                         <div className="grid grid-cols-2 md:grid-cols-6 gap-4 text-center">
                            <div className="bg-slate-50 p-3 rounded-xl border shadow-sm"><div className="text-2xl font-black text-slate-800">{stats.totalSchedules}</div><div className="text-[10px] font-bold uppercase tracking-wider text-slate-500 mt-1">Escalas</div></div>
                            <div className="bg-emerald-50 p-3 rounded-xl border border-emerald-100 shadow-sm"><div className="text-2xl font-black text-emerald-700">{stats.present}</div><div className="text-[10px] font-bold uppercase tracking-wider text-emerald-600 mt-1">Presenças</div></div>
                            <div className="bg-red-50 p-3 rounded-xl border border-red-100 shadow-sm"><div className="text-2xl font-black text-red-700">{stats.absent}</div><div className="text-[10px] font-bold uppercase tracking-wider text-red-600 mt-1">Faltas</div></div>
                            <div className="bg-blue-50 p-3 rounded-xl border border-blue-100 shadow-sm"><div className="text-2xl font-black text-blue-700">{stats.assiduity}%</div><div className="text-[10px] font-bold uppercase tracking-wider text-blue-600 mt-1">Assiduidade</div></div>
                            <div className="bg-blue-50 p-3 rounded-xl border border-blue-100 shadow-sm"><div className="text-2xl font-black text-blue-700">{stats.punctuality}%</div><div className="text-[10px] font-bold uppercase tracking-wider text-blue-600 mt-1">Pontualidade</div></div>
                            <div className="bg-amber-50 p-3 rounded-xl border border-amber-100 shadow-sm"><div className="text-2xl font-black text-amber-700">{stats.performance}</div><div className="text-[10px] font-bold uppercase tracking-wider text-amber-600 mt-1">Nota Média</div></div>
                         </div>
                      </div>
                      <div>
                         <h3 className="font-bold border-b pb-2 mb-4 text-slate-800 text-lg flex items-center gap-2"><FileText className="text-blue-500"/> Protocolos Envolvidos</h3>
                         {stats.memberProtocols.length === 0 ? <p className="text-sm text-slate-500 italic bg-slate-50 p-6 rounded-xl text-center border font-medium shadow-inner">Nenhum protocolo registrado para este membro.</p> : (
                           <div className="space-y-3">{stats.memberProtocols.map(p => (
                               <div key={p.id} className="bg-white border rounded-xl p-4 text-sm flex flex-col md:flex-row justify-between gap-4 items-start md:items-center shadow-sm">
                                 <div className="flex-1 min-w-0">
                                    <div className="flex items-center gap-2 mb-2 flex-wrap">
                                       <span className="font-mono text-xs font-bold text-blue-700 bg-blue-50 border border-blue-200 px-2 py-0.5 rounded shadow-sm">Nº. {p.protocolNumber || 'S/N'}</span>
                                       <strong className="text-slate-800 whitespace-normal break-words text-base">{p.subType}</strong> 
                                       <span className="bg-slate-200 text-slate-600 px-2 py-0.5 rounded text-[10px] uppercase font-bold shrink-0 tracking-wider shadow-sm">{p.type}</span>
                                    </div>
                                    <div className="text-xs text-slate-700 bg-slate-50 border p-2 rounded-lg block whitespace-normal break-words max-w-full font-medium"><b className="text-slate-500 uppercase tracking-wider text-[10px] mr-1">{p.type==='Disciplinar'?'Infração':'Motivo'}:</b> {p.infraction}</div>
                                 </div>
                                 <div className="text-[10px] md:text-right text-slate-600 bg-slate-50 p-3 rounded-lg border w-full md:w-auto mt-2 md:mt-0 flex gap-4 md:block overflow-x-auto shrink-0 shadow-inner">
                                    <div><span className="font-bold text-slate-400 block mb-0.5 uppercase tracking-wider">Início</span><span className="font-bold text-slate-700 text-xs">{formatDateBR(p.startDate)}</span></div>
                                    {p.endDate && <div className="mt-1 md:mt-2"><span className="font-bold text-slate-400 block mb-0.5 uppercase tracking-wider">Fim</span><span className="font-bold text-slate-700 text-xs">{formatDateBR(p.endDate)}</span></div>}
                                 </div>
                               </div>
                           ))}</div>
                         )}
                      </div>
                   </div>
                 );
              })()}
           </div>
        </div>
      )}
    </div>
  );
}

function MemberCard({ m, selected, onToggle, onEdit, onDelete, onInfo }) {
  return (
    <div className={`bg-white rounded-xl border overflow-hidden flex flex-col shadow-sm hover:shadow-md transition-all relative h-full ${selected ? 'border-blue-500 ring-2 ring-blue-200' : 'border-slate-200'}`}>
      <input type="checkbox" className="absolute top-4 right-4 z-10 w-4 h-4 cursor-pointer text-blue-600 rounded focus:ring-blue-500" checked={selected} onChange={(e) => { e.stopPropagation(); onToggle(); }} />
      <div className="p-5 pr-10 flex gap-4 items-center flex-1 cursor-pointer" onClick={onToggle}>
        {m.photoUrl ? <img src={m.photoUrl} className={`w-16 h-16 rounded-full object-cover border-2 shrink-0 ${m.type==='Diretoria'?'border-amber-200':'border-blue-200'}`} /> : <div className={`w-16 h-16 rounded-full flex items-center justify-center font-bold text-xl border-2 shrink-0 ${m.type==='Diretoria'?'bg-amber-50 border-amber-200 text-amber-600':'bg-blue-50 border-blue-200 text-blue-600'}`}>{m.name.substring(0,2)}</div>}
        <div className="flex-1 min-w-0 break-words"><h3 className="font-bold text-lg leading-tight whitespace-normal">{m.usualName || m.name}</h3><span className="text-[10px] font-bold uppercase tracking-wider px-2 py-0.5 mt-1 bg-slate-100 rounded inline-block whitespace-normal text-slate-600 border border-slate-200">{m.role}</span></div>
      </div>
      <div className="flex border-t text-xs text-center font-bold divide-x bg-slate-50 mt-auto">
        <button onClick={onInfo} className="flex-1 py-3 hover:bg-slate-100 flex items-center justify-center gap-1 transition-colors text-slate-700"><Info size={14}/> Info</button>
        <button onClick={onEdit} className="flex-1 py-3 hover:bg-slate-100 flex items-center justify-center gap-1 transition-colors text-slate-700"><Edit size={14}/> Editar</button>
        <button onClick={onDelete} className="flex-1 py-3 text-red-500 hover:bg-red-50 flex items-center justify-center gap-1 transition-colors"><Trash2 size={14}/> Excluir</button>
      </div>
    </div>
  );
}

function BirthdaysView({ members, exMemberIds, viewMode }) {
  const [yearOffset, setYearOffset] = useState(0);
  const currentYear = new Date().getFullYear() + yearOffset;
  const monthsNames = ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'];

  const birthdaysByMonth = useMemo(() => {
    const grouped = {};
    const activeMembers = sortMembers(members.filter(m => !exMemberIds.includes(m.id)));
    activeMembers.forEach(member => {
      if (!member.dob) return;
      const parts = member.dob.split('-');
      if(parts.length !== 3) return;
      const y = parts[0], m = parts[1], d = parts[2];
      const ageToTurn = currentYear - parseInt(y, 10);
      const monthIdx = parseInt(m, 10) - 1;
      if (!grouped[monthIdx]) grouped[monthIdx] = [];
      grouped[monthIdx].push({ ...member, birthDay: parseInt(d,10), ageToTurn });
    });
    Object.values(grouped).forEach(arr => arr.sort((a,b) => a.birthDay - b.birthDay));
    return grouped;
  }, [members, exMemberIds, currentYear]);

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 md:grid-cols-3 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-6">
      <div className="flex justify-between items-center border-b pb-4">
         <h2 className="text-2xl font-bold">Aniversariantes ({currentYear})</h2>
         <div className="flex gap-2">
            <button onClick={()=>setYearOffset(y=>y-1)} className="px-3 py-1.5 bg-slate-200 font-bold rounded-lg text-sm transition-colors hover:bg-slate-300">&lt; Anterior</button>
            <button onClick={()=>setYearOffset(y=>y+1)} className="px-3 py-1.5 bg-slate-200 font-bold rounded-lg text-sm transition-colors hover:bg-slate-300">Próximo &gt;</button>
         </div>
      </div>
      <div className={gridClass}>
        {monthsNames.map((mn, i) => birthdaysByMonth[i] && (
          <div key={mn} className="bg-white rounded-xl border overflow-hidden shadow-sm h-fit">
            <div className="bg-blue-600 text-white p-3 font-bold">{mn}</div>
            <div className="p-4 space-y-3">
              {birthdaysByMonth[i].map(b => (
                <div key={b.id} className="flex items-center gap-3 border-b last:border-b-0 pb-2 last:pb-0 border-slate-100">
                  <div className="bg-blue-50 text-blue-800 w-10 h-10 rounded-lg flex items-center justify-center font-black shrink-0 border border-blue-100 shadow-sm">{b.birthDay}</div>
                  <div className="flex-1 min-w-0 break-words"><h4 className="font-bold text-sm whitespace-normal leading-tight text-slate-800">{b.usualName || b.name}</h4></div>
                  <div className="bg-emerald-50 border border-emerald-100 text-emerald-800 px-2 py-1 rounded text-xs font-bold shrink-0 shadow-sm">{b.ageToTurn} anos</div>
                </div>
              ))}
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

function ImportantDatesView({ customDates, setCustomDates, confirmAction, moveToTrash, viewMode }) {
  const [showForm, setShowForm] = useState(false);
  const [activeSubTab, setActiveSubTab] = useState('pendentes'); 
  const [formData, setFormData] = useState({ id: '', type: 'Aniversário', role: 'Pastor', customName: '', day: '01', month: '01' });
  const [selectedIds, setSelectedIds] = useState([]);
  const currentYear = new Date().getFullYear();
  
  const standardDates = useMemo(() => [
    { id: 'mulher', name: 'Dia Internacional da Mulher', date: `${currentYear}-03-08`, fixed: true },
    { id: 'pascoa', name: 'Páscoa', date: getEaster(currentYear), fixed: true },
    { id: 'maes', name: 'Dia das Mães', date: getNthSunday(currentYear, 5, 2), fixed: true },
    { id: 'pastor', name: 'Dia do Pastor', date: getNthSunday(currentYear, 6, 2), fixed: true },
    { id: 'pais', name: 'Dia dos Pais', date: getNthSunday(currentYear, 8, 2), fixed: true },
    { id: 'missoes', name: 'Dia Nacional de Missões', date: getNthSunday(currentYear, 9, 2), fixed: true },
    { id: 'ebd', name: 'Dia da Escola Dominical', date: getNthSunday(currentYear, 9, 3), fixed: true },
    { id: 'biblia', name: 'Dia Nacional da Bíblia', date: getNthSunday(currentYear, 12, 2), fixed: true },
    { id: 'natal', name: 'Natal', date: `${currentYear}-12-25`, fixed: true },
  ], [currentYear]);

  const allDates = useMemo(() => {
    const safeCustom = getArray(customDates).map(c => ({ ...c, name: c.type === 'Outros' ? c.customName : `${c.type} (${c.role})`, date: `${currentYear}-${c.month}-${c.day}`, fixed: false }));
    return [...standardDates, ...safeCustom].sort((a,b) => new Date(a.date || 0) - new Date(b.date || 0));
  }, [standardDates, customDates, currentYear]);

  const todayStr = getTodayLocalStr();
  const pendingDates = allDates.filter(d => d.date && d.date >= todayStr);
  const completedDates = allDates.filter(d => d.date && d.date < todayStr);

  const displayedList = activeSubTab === 'pendentes' ? pendingDates : completedDates;
  const allVisibleCustomIds = displayedList.filter(d => !d.fixed).map(d => d.id);

  const handleBulkDelete = () => {
     selectedIds.forEach(id => {
        const c = getArray(customDates).find(x => x.id === id);
        if(c) moveToTrash('Data', c);
     });
     setCustomDates(getArray(customDates).filter(c => !selectedIds.includes(c.id)));
     setSelectedIds([]);
  };

  const toggleSelection = (id) => setSelectedIds(prev => prev.includes(id) ? prev.filter(x => x !== id) : [...prev, id]);

  const handleSubmit = (e) => {
    e.preventDefault();
    confirmAction('Deseja salvar esta data importante?', () => {
      const dataToSave = { id: formData.id || generateId(), type: formData.type, role: formData.role, customName: formData.customName, day: formData.day, month: formData.month };
      if (formData.id) setCustomDates(getArray(customDates).map(c => c.id === formData.id ? dataToSave : c)); 
      else setCustomDates([...getArray(customDates), dataToSave]);
      setShowForm(false);
    });
  };

  const daysOpts = Array.from({length: 31}, (_, i) => String(i + 1).padStart(2, '0'));
  const monthOpts = Array.from({length: 12}, (_, i) => String(i + 1).padStart(2, '0'));

  const roleOptionsVida = ['Pastor', 'Esposa do Pastor', 'Coordenador', 'Esposa do Coordenador', 'Presbítero', 'Esposa do Presbítero'];
  const roleOptionsCasamento = ['Pastor e Esposa', 'Coordenador e Esposa', 'Presbítero e Esposa'];

  const handleTypeChange = (val) => {
     setFormData({...formData, type: val, role: val==='Aniversário de Casamento'?roleOptionsCasamento[0]:roleOptionsVida[0]});
  };

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-6">
      <div className="flex justify-between border-b pb-4">
        <h2 className="text-2xl font-bold">Datas Importantes</h2>
        <div className="flex gap-4">
          <div className="bg-slate-200 p-1 rounded-lg flex"><button onClick={()=>setActiveSubTab('pendentes')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='pendentes'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Pendentes</button><button onClick={()=>setActiveSubTab('concluidas')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='concluidas'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Concluídas</button></div>
          {!showForm && <button onClick={() => { setFormData({ id: '', type: 'Aniversário', role: roleOptionsVida[0], customName: '', day: '01', month: '01' }); setShowForm(true); }} className="bg-blue-600 text-white px-4 py-1.5 rounded-lg font-bold flex items-center gap-1 shadow-md hover:bg-blue-700 transition-colors"><Plus size={16}/> Novo</button>}
        </div>
      </div>

      <SelectionBar selectedIds={selectedIds} allIds={allVisibleCustomIds} onSelectAll={()=>setSelectedIds(allVisibleCustomIds)} onDeselectAll={()=>setSelectedIds([])} onBulkDelete={handleBulkDelete} />

      {showForm && (
        <form onSubmit={handleSubmit} className="bg-white p-6 rounded-xl border grid grid-cols-1 sm:grid-cols-2 gap-4 shadow-sm">
           <div><label className="block text-sm font-bold mb-1">Tipo</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.type} onChange={e=>handleTypeChange(e.target.value)}><option>Aniversário</option><option>Aniversário de Casamento</option><option>Outros</option></select></div>
           {formData.type === 'Outros' ? (<div><label className="block text-sm font-bold mb-1">Descrição</label><input required className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" placeholder="Descrição" value={formData.customName} onChange={e=>setFormData({...formData, customName: e.target.value})}/></div>) : (<div><label className="block text-sm font-bold mb-1">Para quem?</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.role} onChange={e=>setFormData({...formData, role: e.target.value})}>{(formData.type==='Aniversário de Casamento'?roleOptionsCasamento:roleOptionsVida).map(r=><option key={r}>{r}</option>)}</select></div>)}
           <div className="col-span-1 sm:col-span-2">
              <label className="block text-sm font-bold mb-1">Data Anual (Dia / Mês)</label>
              <div className="flex gap-2 items-center">
                 <select className="p-2 border rounded font-bold bg-slate-50 text-slate-700" value={formData.day} onChange={e=>setFormData({...formData, day: e.target.value})}>{daysOpts.map(d=><option key={d} value={d}>{d}</option>)}</select>
                 <span className="font-bold text-slate-400">/</span>
                 <select className="p-2 border rounded font-bold bg-slate-50 text-slate-700" value={formData.month} onChange={e=>setFormData({...formData, month: e.target.value})}>{monthOpts.map(m=><option key={m} value={m}>{m}</option>)}</select>
              </div>
           </div>
           <div className="col-span-1 sm:col-span-2 text-right flex justify-end gap-2 mt-4 border-t pt-4">
             <button type="button" onClick={() => setShowForm(false)} className="px-6 py-2 border rounded-lg text-slate-600 font-bold hover:bg-slate-50 transition-colors">Cancelar</button>
             <button type="submit" className="bg-slate-900 text-white px-6 py-2 rounded-lg font-bold shadow-md hover:bg-slate-800 transition-colors">Salvar</button>
           </div>
        </form>
      )}
      <div className={gridClass}>
        {displayedList.map(d => (
          <div key={d.id} className={`bg-white p-4 rounded-xl border flex items-center gap-4 shadow-sm hover:shadow-md transition-all relative ${selectedIds.includes(d.id) ? 'border-blue-500 ring-2 ring-blue-200' : 'border-slate-200'}`}>
             {!d.fixed && <input type="checkbox" className="absolute top-2 right-2 z-10 cursor-pointer w-4 h-4 text-blue-600 rounded" checked={selectedIds.includes(d.id)} onChange={()=>toggleSelection(d.id)} />}
             <div className="bg-blue-50 text-blue-600 p-2 rounded-lg text-center w-16 shrink-0 flex flex-col justify-center items-center border border-blue-100">
                <span className="text-[10px] font-bold uppercase tracking-wider mb-0.5">{getWeekdayShort(d.date)}</span>
                <span className="font-black text-2xl leading-none">{d.date.split('-')[2]}</span>
             </div>
             <div className="flex-1 min-w-0 break-words pr-4">
                <h4 className="font-bold text-sm text-slate-800 whitespace-normal leading-tight" title={d.name}>{d.name}</h4>
                <p className="text-[10px] uppercase font-bold text-slate-400 mt-1">{getMonthName(d.date)}</p>
             </div>
             {!d.fixed && (
                <button onClick={()=>confirmAction('Remover para lixeira?', ()=> { moveToTrash('Data', d); setCustomDates(getArray(customDates).filter(c=>c.id!==d.id)); setSelectedIds(prev=>prev.filter(id=>id!==d.id)); })} className="p-2 text-slate-400 hover:text-red-500 hover:bg-red-50 rounded-lg transition-colors shrink-0">
                   <Trash2 size={18} />
                </button>
             )}
          </div>
        ))}
        {displayedList.length === 0 && !showForm && <div className="col-span-full py-12 text-center text-slate-500">Nenhuma data encontrada.</div>}
      </div>
    </div>
  );
}

// --- View: Escalas ---
function SchedulesView({ members, exMemberIds, unavailableMemberIds, schedules, setSchedules, attendances, setAttendances, postings, setPostings, confirmAction, moveToTrash, viewMode, festivities }) {
  const [showForm, setShowForm] = useState(false);
  const [activeSubTab, setActiveSubTab] = useState('mes');
  const [expandedScheduleId, setExpandedScheduleId] = useState(null);
  const [expandedPostingsId, setExpandedPostingsId] = useState(null);
  
  const defaultFormData = { id: '', festivityId: '', date: '', time: '', eventName: '', assigned: [], support: [], supervisor: [], liveAssignees: [], category: 'Culto Normativo', consagracaoOrgao: '', isRepeat: false, freq: 'Semanal', monthlyType: 'dia_especifico', endCondition: 'nunca', endDateValue: '', endOccurrences: '2' };
  const [formData, setFormData] = useState(defaultFormData);
  const [selectedIds, setSelectedIds] = useState([]);

  const activeMembers = sortMembers(members.filter(m => !exMemberIds.includes(m.id)));
  const formEfetivos = activeMembers.filter(m => m.type === 'Efetivo' && (!unavailableMemberIds.includes(m.id) || getArray(formData.assigned).includes(m.id) || getArray(formData.support).includes(m.id) || getArray(formData.liveAssignees).includes(m.id)));
  const formDiretoria = activeMembers.filter(m => m.type === 'Diretoria' && (!unavailableMemberIds.includes(m.id) || getArray(formData.supervisor).includes(m.id) || getArray(formData.liveAssignees).includes(m.id)));
  
  const pendingSchedules = schedules.filter(s => !s.completed).sort((a,b) => new Date(a.date || 0) - new Date(b.date || 0));
  const currentMonthStr = getTodayLocalStr().substring(0, 7);
  
  const displayedSchedules = pendingSchedules.filter(s => {
    if (!s.date) return false;
    if (activeSubTab === 'mes') return s.date.startsWith(currentMonthStr);
    return !s.date.startsWith(currentMonthStr) && s.date > getTodayLocalStr();
  });

  const allVisibleIds = displayedSchedules.map(s => s.id);

  const handleBulkDelete = () => {
     selectedIds.forEach(id => {
        const s = schedules.find(x => x.id === id);
        if(s) moveToTrash('Escala', s);
     });
     setSchedules(schedules.filter(s => !selectedIds.includes(s.id)));
     setSelectedIds([]);
  };

  const toggleSelection = (id) => setSelectedIds(prev => prev.includes(id) ? prev.filter(x => x !== id) : [...prev, id]);

  const handleSubmit = (e) => {
    e.preventDefault();
    confirmAction('Deseja salvar as informações desta Escala?', () => {
      const isCustomCulto = formData.category === 'Culto Normativo' && formData.eventName === 'Consagração';
      const fName = isCustomCulto && formData.consagracaoOrgao ? `Consagração (${formData.consagracaoOrgao})` : formData.eventName;
      
      const baseScale = { ...formData, eventName: fName, completed: formData.completed || false, category: formData.category || 'Culto Normativo' };
      delete baseScale.isRepeat; delete baseScale.freq; delete baseScale.monthlyType; delete baseScale.endCondition; delete baseScale.endDateValue; delete baseScale.endOccurrences;

      if (formData.id) {
         setSchedules(schedules.map(s => s.id === formData.id ? baseScale : s));
      } else {
         const newSchedules = [{ ...baseScale, id: generateId() }];
         
         // Repetição Avançada de Escalas
         if (formData.isRepeat && formData.date) {
            let currentDt = new Date(formData.date + 'T00:00:00');
            let occurrences = 1;
            const limitOccurrences = formData.endCondition === 'ocorrencias' ? parseInt(formData.endOccurrences, 10) + 1 : (formData.endCondition === 'nunca' ? 30 : 100); 
            const limitDate = formData.endCondition === 'data' && formData.endDateValue ? new Date(formData.endDateValue + 'T23:59:59') : null;
            
            const origDay = currentDt.getDate();
            const origWeekday = currentDt.getDay();
            const origNth = Math.ceil(origDay / 7);

            while(occurrences < limitOccurrences) {
               let nDt = new Date(currentDt);
               if (formData.freq === 'Diária') nDt.setDate(nDt.getDate() + 1);
               else if (formData.freq === 'Semanal') nDt.setDate(nDt.getDate() + 7);
               else if (formData.freq === 'Anual') nDt.setFullYear(nDt.getFullYear() + 1);
               else if (formData.freq === 'Mensal') {
                  if (formData.monthlyType === 'dia_especifico') {
                     nDt.setMonth(nDt.getMonth() + 1);
                     if (nDt.getDate() !== origDay) nDt.setDate(0); 
                  } else {
                     nDt.setMonth(nDt.getMonth() + 1);
                     nDt.setDate(1);
                     let firstWeekday = nDt.getDay();
                     let offset = (origWeekday - firstWeekday + 7) % 7;
                     let targetDate = 1 + offset + ((origNth - 1) * 7);
                     const daysInMonth = new Date(nDt.getFullYear(), nDt.getMonth() + 1, 0).getDate();
                     if (targetDate > daysInMonth) targetDate -= 7;
                     nDt.setDate(targetDate);
                  }
               }

               if (limitDate && nDt > limitDate) break;

               const dStr = `${nDt.getFullYear()}-${String(nDt.getMonth()+1).padStart(2,'0')}-${String(nDt.getDate()).padStart(2,'0')}`;
               newSchedules.push({...baseScale, id: generateId(), date: dStr});
               currentDt = nDt;
               occurrences++;
            }
         }
         
         setSchedules([...schedules, ...newSchedules]);
      }
      setShowForm(false);
    });
  };

  const getScheduleCompletion = (s) => {
    const inv = [...new Set([...getArray(s.assigned), ...getArray(s.support), ...getArray(s.supervisor), ...getArray(s.liveAssignees)])].filter(Boolean);
    if (inv.length === 0) return null;
    let reg = 0; inv.forEach(id => { if (attendances[s.id]?.[id]?.status) reg++; });
    return { reg, total: inv.length, pct: Math.round((reg/inv.length)*100) };
  };

  const getMemberName = (id) => {
     const m = members.find(x => x.id === id);
     return m ? (m.usualName || m.name) : 'Membro Excluído';
  };

  const handleDuplicate = (s) => {
     setFormData({...s, id: '', date: getTodayLocalStr(), isRepeat: false, freq: 'Semanal', monthlyType: 'dia_especifico', endCondition: 'nunca', endDateValue: '', endOccurrences: '2'});
     setShowForm(true);
  };

  const linkedFestivity = festivities?.find(f => f.id === formData.festivityId);
  const showLiveField = formData.category === 'Festividade' && linkedFestivity?.requireLive;

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 lg:grid-cols-2 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-6 relative animate-in fade-in duration-300">
      <div className="flex justify-between border-b pb-4">
        <h2 className="text-2xl font-bold">Escalas & Frequência</h2>
        <div className="flex gap-4">
           <div className="bg-slate-200 p-1 rounded-lg flex"><button onClick={()=>setActiveSubTab('mes')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='mes'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Escalas do Mês</button><button onClick={()=>setActiveSubTab('futuras')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='futuras'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Escalas Futuras</button></div>
           {!showForm && <button onClick={() => { setFormData(defaultFormData); setShowForm(true); }} className="bg-blue-600 text-white px-4 py-1.5 rounded-lg font-bold flex items-center gap-1 shadow-md hover:bg-blue-700 transition-colors"><Plus size={16}/> Nova</button>}
        </div>
      </div>

      <SelectionBar selectedIds={selectedIds} allIds={allVisibleIds} onSelectAll={()=>setSelectedIds(allVisibleIds)} onDeselectAll={()=>setSelectedIds([])} onBulkDelete={handleBulkDelete} />
      
      {showForm && (
        <form onSubmit={handleSubmit} className="bg-white p-6 rounded-xl border space-y-6 shadow-sm">
          <div className="grid grid-cols-1 sm:grid-cols-4 gap-4">
             <div><label className="block text-sm font-bold mb-1">Data</label><input required type="date" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.date} onChange={e=>setFormData({...formData, date: e.target.value})}/></div>
             <div><label className="block text-sm font-bold mb-1">Horário</label><input required type="time" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.time} onChange={e=>setFormData({...formData, time: e.target.value})}/></div>
             
             {/* Categoria Inalterável para Criação Manual, mas mantendo a originada de eventos automáticos */}
             <div>
                <label className="block text-sm font-bold mb-1">Categoria</label>
                <input disabled className="w-full p-2 border rounded font-bold text-slate-500 bg-slate-100 cursor-not-allowed" value={formData.category} />
             </div>
             
             <div>
                 <label className="block text-sm font-bold mb-1">Culto/Evento (Título)</label>
                 {formData.category === 'Culto Normativo' ? (
                     <select required className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.eventName} onChange={e=>setFormData({...formData, eventName: e.target.value})}>{optionsCultos.map(c=><option key={c}>{c}</option>)}{formData.eventName && !optionsCultos.includes(formData.eventName) && formData.eventName!=='Consagração' && <option>{formData.eventName}</option>}</select>
                 ) : (
                     <input disabled className="w-full p-2 border rounded font-bold text-slate-500 bg-slate-100 cursor-not-allowed" value={formData.eventName} />
                 )}
             </div>
             
             {formData.category === 'Culto Normativo' && formData.eventName === 'Consagração' && (
                <div className="col-span-1 sm:col-span-4 p-3 bg-slate-50 border rounded-xl"><label className="block text-sm font-bold mb-1">Órgão da Consagração</label><select required className="w-full p-2 border rounded font-bold text-slate-700 bg-white" value={formData.consagracaoOrgao} onChange={e=>setFormData({...formData, consagracaoOrgao: e.target.value})}><option value="">Selecione...</option>{consagraOrgaos.map(o=><option key={o}>{o}</option>)}</select></div>
             )}

             {!formData.id && formData.category === 'Culto Normativo' && (
                <div className="col-span-1 sm:col-span-4 p-4 border rounded-xl bg-slate-50">
                   <label className="flex items-center gap-2 font-bold text-slate-800 mb-2"><input type="checkbox" className="w-4 h-4 rounded text-blue-600 cursor-pointer" checked={formData.isRepeat} onChange={e=>setFormData({...formData, isRepeat: e.target.checked})}/> Programar Repetição de Escala</label>
                   {formData.isRepeat && (
                      <div className="grid grid-cols-1 sm:grid-cols-3 gap-4 mt-3 pt-3 border-t border-slate-200">
                         <div>
                            <label className="block text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Frequência</label>
                            <select className="w-full p-2 border rounded font-bold text-slate-700 bg-white" value={formData.freq} onChange={e=>setFormData({...formData, freq: e.target.value})}>
                               <option>Diária</option><option>Semanal</option><option>Mensal</option><option>Anual</option>
                            </select>
                         </div>
                         {formData.freq === 'Mensal' && (
                            <div>
                               <label className="block text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Repetir no</label>
                               <select className="w-full p-2 border rounded font-bold text-slate-700 bg-white" value={formData.monthlyType} onChange={e=>setFormData({...formData, monthlyType: e.target.value})}>
                                  <option value="dia_especifico">Mesmo dia do mês</option>
                                  <option value="dia_semana">Mesmo dia da semana (Ex: 2º Dom)</option>
                               </select>
                            </div>
                         )}
                         <div>
                            <label className="block text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Término da Repetição</label>
                            <select className="w-full p-2 border rounded font-bold text-slate-700 bg-white" value={formData.endCondition} onChange={e=>setFormData({...formData, endCondition: e.target.value, endDateValue: '', endOccurrences: '2'})}>
                               <option value="nunca">Nunca (Padrão - Limite de Lote)</option>
                               <option value="data">Em (Data Específica)</option>
                               <option value="ocorrencias">Após (X Ocorrências)</option>
                            </select>
                         </div>
                         {formData.endCondition === 'data' && (
                            <div>
                               <label className="block text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Data Final</label>
                               <input type="date" required min={formData.date} className="w-full p-2 border rounded font-bold text-slate-700 bg-white" value={formData.endDateValue} onChange={e=>setFormData({...formData, endDateValue: e.target.value})}/>
                            </div>
                         )}
                         {formData.endCondition === 'ocorrencias' && (
                            <div>
                               <label className="block text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Ocorrências Extras</label>
                               <input type="number" required min="1" max="100" className="w-full p-2 border rounded font-bold text-slate-700 bg-white" value={formData.endOccurrences} onChange={e=>setFormData({...formData, endOccurrences: e.target.value})}/>
                            </div>
                         )}
                      </div>
                   )}
                </div>
             )}
          </div>
          <div className={`grid grid-cols-1 md:grid-cols-3 ${showLiveField ? 'xl:grid-cols-4' : ''} gap-6`}>
             <div className="bg-slate-50 p-4 rounded border"><h4 className="font-bold mb-2 text-slate-800 text-sm">Escalados (Efetivo)</h4><div className="max-h-48 overflow-y-auto space-y-1">{formEfetivos.map(m => <label key={m.id} className="flex gap-2 text-xs font-medium text-slate-700 items-center"><input type="checkbox" disabled={getArray(formData.support).includes(m.id)} checked={getArray(formData.assigned).includes(m.id)} onChange={() => setFormData(f => ({...f, assigned: f.assigned.includes(m.id) ? f.assigned.filter(i=>i!==m.id) : [...f.assigned, m.id]}))} />{m.usualName || m.name}</label>)}</div></div>
             <div className="bg-slate-50 p-4 rounded border"><h4 className="font-bold mb-2 text-slate-800 text-sm">Apoio (Efetivo)</h4><div className="max-h-48 overflow-y-auto space-y-1">{formEfetivos.map(m => <label key={m.id} className="flex gap-2 text-xs font-medium text-slate-700 items-center"><input type="checkbox" disabled={getArray(formData.assigned).includes(m.id)} checked={getArray(formData.support).includes(m.id)} onChange={() => setFormData(f => ({...f, support: f.support.includes(m.id) ? f.support.filter(i=>i!==m.id) : [...f.support, m.id]}))} />{m.usualName || m.name}</label>)}</div></div>
             <div className="bg-slate-50 p-4 rounded border"><h4 className="font-bold mb-2 text-slate-800 text-sm">Vistoria (Diretoria)</h4><div className="max-h-48 overflow-y-auto space-y-1">{formDiretoria.map(m => <label key={m.id} className="flex gap-2 text-xs font-medium text-slate-700 items-center"><input type="checkbox" checked={getArray(formData.supervisor).includes(m.id)} onChange={() => setFormData(f => ({...f, supervisor: f.supervisor.includes(m.id) ? f.supervisor.filter(i=>i!==m.id) : [...f.supervisor, m.id]}))} />{m.usualName || m.name}</label>)}</div></div>
             
             {showLiveField && (
                <div className="bg-red-50/50 p-4 rounded border border-red-100"><h4 className="font-bold mb-2 text-red-800 text-sm flex items-center gap-1"><Video size={14}/> Live (Todos)</h4><div className="max-h-48 overflow-y-auto space-y-1">{activeMembers.filter(m=>!unavailableMemberIds.includes(m.id)).map(m => <label key={m.id} className="flex gap-2 text-xs font-medium text-slate-700 items-center"><input type="checkbox" checked={getArray(formData.liveAssignees).includes(m.id)} onChange={() => setFormData(f => ({...f, liveAssignees: f.liveAssignees.includes(m.id) ? f.liveAssignees.filter(i=>i!==m.id) : [...f.liveAssignees, m.id]}))} />{m.usualName || m.name}</label>)}</div></div>
             )}
          </div>
          <div className="text-right flex justify-end gap-2 mt-4 border-t pt-4">
             <button type="button" onClick={() => setShowForm(false)} className="px-6 py-2 border rounded-lg text-slate-600 font-bold hover:bg-slate-50 transition-colors">Cancelar</button>
             <button type="submit" className="bg-slate-900 text-white px-6 py-2 rounded-lg font-bold shadow-md hover:bg-slate-800 transition-colors">Salvar Escala</button>
          </div>
        </form>
      )}
      <div className={gridClass}>
         {displayedSchedules.map(s => {
            const comp = getScheduleCompletion(s);
            const isAttComplete = comp && comp.pct === 100;
            const isPostComplete = postings[s.id]?.isSaved;
            const isExpandedAtt = expandedScheduleId === s.id;
            const isExpandedPost = expandedPostingsId === s.id;
            
            // Regra de Botão de Chamada: Apenas se tiver Escalado E Vistoria
            const canCall = getArray(s.assigned).length > 0 && getArray(s.supervisor).length > 0;

            // Formatação Visual de Categoria
            let catColor = "text-blue-700 bg-blue-100 border-blue-300";
            let catName = s.category || 'Culto Normativo';
            if (catName === 'Festividade') catColor = "text-orange-700 bg-orange-100 border-orange-300";
            else if (catName === 'Encontro') catColor = "text-pink-700 bg-pink-100 border-pink-300";
            else if (catName === 'Eventos' || catName === 'Evento') catColor = "text-emerald-700 bg-emerald-100 border-emerald-300";

            const evtCode = getEventCode(s.eventName);

            return (
              <div key={s.id} className={`bg-white rounded-xl border overflow-hidden shadow-sm hover:shadow-md transition-all h-full flex flex-col relative ${selectedIds.includes(s.id) ? 'border-blue-500 ring-2 ring-blue-200' : 'border-slate-200'}`}>
                 <input type="checkbox" className="absolute top-4 right-4 z-10 w-4 h-4 cursor-pointer text-blue-600 rounded" checked={selectedIds.includes(s.id)} onChange={(e)=>{e.stopPropagation(); toggleSelection(s.id);}} />
                 <div className="p-4 flex flex-col sm:flex-row gap-4 items-start flex-1 cursor-pointer" onClick={()=>toggleSelection(s.id)}>
                    <div className="bg-blue-50 text-blue-800 p-2 rounded-lg text-center w-20 shrink-0 flex flex-col justify-center items-center border border-blue-100">
                       <span className="text-[10px] font-bold uppercase tracking-wider mb-0.5">{getWeekdayShort(s.date)}</span>
                       <span className="font-black text-2xl leading-none">{s.date.split('-')[2]}</span>
                       <span className="text-[10px] font-bold uppercase mt-0.5 mb-1.5">{getMonthName(s.date).substring(0,3)}</span>
                       <div className="w-full border-t border-blue-200/50 pt-1.5"><span className="text-sm font-black text-blue-700">{s.time}</span></div>
                    </div>
                    <div className="flex-1 w-full min-w-0 break-words flex flex-col h-full pr-6">
                       <div className="flex gap-2 mb-2 flex-wrap">
                          <div className={`text-xs font-black uppercase px-2 py-1 rounded border ${catColor}`}>{catName}</div>
                          {evtCode && <div className={`text-xs font-black uppercase px-2 py-1 rounded border ${catColor}`}>CÓD. {evtCode}</div>}
                       </div>
                       <h3 className="font-bold text-lg mb-3 text-slate-800 whitespace-normal leading-tight" title={s.eventName}>{s.eventName}</h3>
                       <div className="grid grid-cols-1 sm:grid-cols-4 gap-2 text-sm text-slate-700 flex-1">
                          <div className="bg-slate-50 border rounded p-2"><span className="block text-[10px] font-bold text-slate-400 mb-1 uppercase tracking-wider">Escalados ({getArray(s.assigned).length})</span><ul className="space-y-1 mt-1">{getArray(s.assigned).map(id=><li key={id} className="font-medium whitespace-normal leading-tight block">{getMemberName(id)}</li>)}{getArray(s.assigned).length===0&&<li className="italic text-slate-400 text-xs">Nenhum</li>}</ul></div>
                          <div className="bg-slate-50 border rounded p-2"><span className="block text-[10px] font-bold text-slate-400 mb-1 uppercase tracking-wider">Apoio ({getArray(s.support).length})</span><ul className="space-y-1 mt-1">{getArray(s.support).map(id=><li key={id} className="font-medium whitespace-normal leading-tight block">{getMemberName(id)}</li>)}{getArray(s.support).length===0&&<li className="italic text-slate-400 text-xs">Nenhum</li>}</ul></div>
                          <div className="bg-slate-50 border rounded p-2"><span className="block text-[10px] font-bold text-slate-400 mb-1 uppercase tracking-wider">Vistoria ({getArray(s.supervisor).length})</span><ul className="space-y-1 mt-1">{getArray(s.supervisor).map(id=><li key={id} className="font-medium whitespace-normal leading-tight block">{getMemberName(id)}</li>)}{getArray(s.supervisor).length===0&&<li className="italic text-slate-400 text-xs">Nenhum</li>}</ul></div>
                          {getArray(s.liveAssignees).length > 0 && (
                             <div className="bg-red-50/50 border border-red-100 rounded p-2"><span className="block text-[10px] font-bold text-red-500 mb-1 uppercase tracking-wider">Live ({getArray(s.liveAssignees).length})</span><ul className="space-y-1 mt-1">{getArray(s.liveAssignees).map(id=><li key={id} className="font-medium whitespace-normal leading-tight block">{getMemberName(id)}</li>)}</ul></div>
                          )}
                       </div>
                    </div>
                 </div>
                 <div className="p-4 border-t bg-slate-50 flex gap-2 shrink-0 flex-wrap justify-between mt-auto">
                    <div className="flex gap-2 w-full lg:w-auto">
                       <button disabled={!canCall} onClick={()=> {setExpandedScheduleId(isExpandedAtt?null:s.id);setExpandedPostingsId(null);}} className={`flex-1 lg:flex-none px-4 py-2 text-sm font-bold border rounded-lg transition-colors disabled:opacity-50 disabled:cursor-not-allowed ${isExpandedAtt?'bg-blue-100 text-blue-800 border-blue-200':'bg-white hover:bg-slate-100 text-slate-700'}`}>Chamada {comp?`(${comp.reg}/${comp.total})`:''}</button>
                       <button disabled={!isAttComplete} onClick={()=> {setExpandedPostingsId(isExpandedPost?null:s.id);setExpandedScheduleId(null);}} className={`flex-1 lg:flex-none px-4 py-2 text-sm font-bold border rounded-lg transition-colors disabled:opacity-50 disabled:cursor-not-allowed ${isExpandedPost?'bg-pink-100 text-pink-800 border-pink-200':'bg-white hover:bg-slate-100 text-slate-700'}`}>Postagens</button>
                    </div>
                    <div className="flex gap-2 w-full lg:w-auto mt-2 lg:mt-0 flex-wrap">
                       <button disabled={!isAttComplete || !isPostComplete} onClick={()=>confirmAction('Deseja concluir a escala e enviar para o histórico?', ()=>setSchedules(schedules.map(sc=>sc.id===s.id?{...sc,completed:true}:sc)))} className="flex-1 lg:flex-none px-4 py-2 text-sm font-bold border rounded-lg bg-emerald-50 text-emerald-600 disabled:opacity-50 transition-colors hover:bg-emerald-100">Concluir</button>
                       <button onClick={()=>handleDuplicate(s)} className="flex-1 lg:flex-none px-3 py-2 bg-white border rounded-lg font-bold text-sm text-slate-600 flex items-center justify-center gap-1 transition-colors hover:bg-slate-50" title="Duplicar"><CopyPlus size={16}/></button>
                       <button onClick={()=>{setFormData(s);setShowForm(true);}} className="flex-1 lg:flex-none px-3 py-2 bg-white border rounded-lg font-bold text-sm text-slate-600 flex items-center justify-center gap-1 transition-colors hover:bg-slate-50" title="Editar"><Edit size={16}/></button>
                       <button onClick={()=>confirmAction('Remover para a lixeira?', ()=>{moveToTrash('Escala', s); setSchedules(schedules.filter(sc=>sc.id!==s.id));})} className="flex-1 lg:flex-none px-3 py-2 bg-white border rounded-lg font-bold text-sm text-red-500 flex items-center justify-center gap-1 transition-colors hover:bg-red-50" title="Excluir"><Trash2 size={16}/></button>
                    </div>
                 </div>
                 {isExpandedAtt && <AttendancePanel schedule={s} members={members} attendances={attendances} setAttendances={setAttendances} onClose={()=>setExpandedScheduleId(null)} confirmAction={confirmAction}/>}
                 {isExpandedPost && <PostingsPanel schedule={s} postings={postings} setPostings={setPostings} onClose={()=>setExpandedPostingsId(null)} confirmAction={confirmAction}/>}
              </div>
            )
         })}
         {displayedSchedules.length === 0 && !showForm && <div className="col-span-full py-12 text-center text-slate-500">Nenhuma escala {activeSubTab==='mes'?'neste mês':'futura'}.</div>}
      </div>
    </div>
  );
}

function AttendancePanel({ schedule, members, attendances, setAttendances, onClose, confirmAction }) {
  const [localData, setLocalData] = useState(attendances[schedule.id] || {});
  const [hasChanges, setHasChanges] = useState(false);
  const involved = [...new Set([...getArray(schedule.assigned), ...getArray(schedule.support), ...getArray(schedule.supervisor), ...getArray(schedule.liveAssignees)])].filter(Boolean);

  const handleSave = () => { 
    confirmAction('Salvar esta frequência?', () => {
      setAttendances(p => ({...p, [schedule.id]: localData})); setHasChanges(false); onClose(); 
    });
  };

  return (
    <div className="bg-slate-50 p-4 border-t border-slate-200">
       <div className="flex justify-between mb-4 font-bold"><h4 className="flex items-center gap-2"><UserCheck/> Lista de Chamada</h4> <button onClick={handleSave} disabled={!hasChanges} className="bg-blue-600 text-white px-4 py-1.5 rounded-lg disabled:opacity-50 text-sm font-bold shadow-md hover:bg-blue-700 transition-colors">Salvar Frequência</button></div>
       <div className="overflow-x-auto border rounded-xl bg-white shadow-sm">
         <table className="w-full text-left">
            <thead><tr className="bg-slate-100 text-sm border-b"><th className="p-3">Nome</th><th className="p-3 w-40">Status</th><th className="p-3 w-32">Chegada</th><th className="p-3">Info/Avaliação</th></tr></thead>
            <tbody>
               {involved.map(id => {
                  const m = members.find(x=>x.id===id); if(!m) return null;
                  const rec = localData[id] || { status:'', arrivalTime:'', justification:'', rating:0 };
                  return (
                    <tr key={id} className="border-b last:border-b-0 text-sm hover:bg-slate-50 transition-colors">
                       <td className="p-3 font-bold text-slate-800 break-words whitespace-normal">{m.usualName || m.name}</td>
                       <td className="p-3"><select className="w-full border border-slate-300 rounded p-1.5 font-bold text-slate-600 bg-white" value={rec.status} onChange={e=>{setLocalData(l=>({...l,[id]:{...rec,status:e.target.value}}));setHasChanges(true);}}><option value="">Pendente</option><option>Presente</option><option>Ausente</option><option>Justificado</option></select></td>
                       <td className="p-3"><input type="time" disabled={rec.status!=='Presente'} className="w-full border border-slate-300 rounded p-1.5 disabled:opacity-50 disabled:bg-slate-100 font-medium" value={rec.arrivalTime} onChange={e=>{setLocalData(l=>({...l,[id]:{...rec,arrivalTime:e.target.value}}));setHasChanges(true);}} /></td>
                       <td className="p-3">
                         {rec.status === 'Presente' ? (
                            <div className="flex gap-1">{[1,2,3,4,5].map(v=><button key={v} onClick={()=>{setLocalData(l=>({...l,[id]:{...rec,rating:v}}));setHasChanges(true);}}><Star size={18} className={v<=rec.rating?"text-amber-400 fill-amber-400 transition-colors":"text-slate-200 transition-colors hover:text-amber-300"}/></button>)}</div>
                         ) : (<input placeholder="Motivo" disabled={rec.status!=='Justificado'} className="w-full border border-slate-300 rounded p-1.5 disabled:opacity-50 disabled:bg-slate-100" value={rec.justification} onChange={e=>{setLocalData(l=>({...l,[id]:{...rec,justification:e.target.value}}));setHasChanges(true);}} />)}
                       </td>
                    </tr>
                  )
               })}
            </tbody>
         </table>
       </div>
    </div>
  );
}

function PostingsPanel({ schedule, postings, setPostings, onClose, confirmAction }) {
  const [localData, setLocalData] = useState(postings[schedule.id] || { stories: [], fotos: [], bibleBook: '', bibleChapter: '', verseStart: '', verseEnd: '', readerName: '', plusOneVerse: false, isSaved: false });
  const isFestivity = !!schedule.festivityId;
  const eName = schedule.eventName || '';
  
  let storyOptions = [];
  let fotoOptions = [];

  if (isFestivity) {
    storyOptions = ['Chamada', 'Hinos Congregacionais', 'Órgão Aniversariante', 'Órgão Visitante I', 'Órgão Visitante II', 'Órgão Visitante III', 'Momento da Palavra'];
    fotoOptions = ['Dirigente do Trabalho', 'Leitura Oficial', 'Nave do Templo', 'Órgão Aniversariante', 'Órgãos Visitantes', 'Preletor', 'Conversões'];
  } else {
    const isMocidade = eName.includes('Mocidade') || eName.includes('Santa Ceia');
    storyOptions = ['Chamada', 'Hinos Congregacionais', 'Conj. Musical', 'Coral', 'Conj. Infantil', isMocidade ? 'Mocidade' : 'PROATI', 'União', 'Grupo Jovem', 'Eletrônico', 'Momento de Adoração', 'Momento da Palavra'];
    fotoOptions = ['Dirigente do Trabalho', 'Leitura Oficial', 'Oportunidades', 'Nave do Templo'];
    if (eName.includes('PROATI')) fotoOptions.push('PROATI');
    if (eName.includes('Mocidade')) fotoOptions.push('Mocidade');
    if (eName.includes('Infantil')) fotoOptions.push('Conj. Infantil');
    fotoOptions.push('Preletor', 'Conversões');
  }

  const verseOptions = Array.from({length: 176}, (_, i) => i + 1); // 176 é max (Salmo 119)
  
  const toggleArr = (f, v) => setLocalData(l => ({ ...l, [f]: l[f].includes(v) ? l[f].filter(x=>x!==v) : [...l[f], v] }));
  
  const handleSave = () => { 
    confirmAction('Salvar relatório de postagens?', () => {
      setPostings(p => ({...p, [schedule.id]: {...localData, isSaved:true}})); onClose(); 
    });
  };

  return (
    <div className="bg-slate-50 p-4 border-t space-y-4">
      <div className="flex justify-between font-bold text-slate-800"><h4 className="flex items-center gap-2"><Smartphone size={18}/> Registros de Mídia</h4><button onClick={handleSave} className="bg-blue-600 text-white px-4 py-1.5 rounded-lg text-sm shadow-md hover:bg-blue-700 transition-colors">Salvar Postagens</button></div>
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
         <div className="bg-white p-4 border rounded-xl shadow-sm"><h5 className="font-bold text-sm mb-3 text-pink-500 border-b pb-1">Story's</h5><div className="grid grid-cols-1 sm:grid-cols-2 gap-2">{storyOptions.map(o=><label key={o} className="flex items-center gap-2 text-sm text-slate-700 cursor-pointer hover:text-slate-900 whitespace-normal break-words"><input type="checkbox" className="w-4 h-4 text-pink-500 rounded focus:ring-pink-500 shrink-0" checked={getArray(localData.stories).includes(o)} onChange={()=>toggleArr('stories',o)}/>{o}</label>)}</div></div>
         <div className="bg-white p-4 border rounded-xl shadow-sm"><h5 className="font-bold text-sm mb-3 text-emerald-500 border-b pb-1">Fotos Feed</h5><div className="grid grid-cols-1 sm:grid-cols-2 gap-2">{fotoOptions.map(o=><label key={o} className="flex items-center gap-2 text-sm text-slate-700 cursor-pointer hover:text-slate-900 whitespace-normal break-words"><input type="checkbox" className="w-4 h-4 text-emerald-500 rounded focus:ring-emerald-500 shrink-0" checked={getArray(localData.fotos).includes(o)} onChange={()=>toggleArr('fotos',o)}/>{o}</label>)}</div></div>
         <div className="col-span-1 md:col-span-2 bg-white p-4 border rounded-xl shadow-sm grid grid-cols-1 sm:grid-cols-5 gap-4 items-end">
            <div className="col-span-1 sm:col-span-5 flex justify-between items-center border-b pb-1">
               <h5 className="font-bold text-sm text-amber-600">Leitura Oficial</h5>
               <label className="flex items-center gap-1.5 text-xs font-bold text-slate-600 cursor-pointer"><input type="checkbox" className="w-3.5 h-3.5 rounded text-blue-600" checked={localData.plusOneVerse} onChange={e=>setLocalData({...localData, plusOneVerse: e.target.checked, verseEnd: ''})}/> + 1 Versículo</label>
            </div>
            <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Livro</label><select className="w-full border p-2 text-sm rounded-lg font-bold text-slate-700 bg-slate-50" value={localData.bibleBook} onChange={e=>setLocalData({...localData,bibleBook:e.target.value, bibleChapter:'', verseStart: '', verseEnd: ''})}><option value="">Livro</option>{bibleBooks.map(b=><option key={b}>{b}</option>)}</select></div>
            <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Capítulo</label><select disabled={!localData.bibleBook} className="w-full border p-2 text-sm rounded-lg disabled:bg-slate-100 font-bold text-slate-700 bg-slate-50" value={localData.bibleChapter} onChange={e=>setLocalData({...localData,bibleChapter:e.target.value})}><option value="">Cap.</option>{localData.bibleBook && Array.from({length:bibleChaptersCount[localData.bibleBook]||0},(_,i)=><option key={i+1}>{i+1}</option>)}</select></div>
            <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Vers. Início</label><select disabled={!localData.bibleChapter} className="w-full border p-2 text-sm rounded-lg disabled:bg-slate-100 font-bold text-slate-700 bg-slate-50" value={localData.verseStart} onChange={e=>setLocalData({...localData, verseStart: e.target.value})}><option value="">Início</option>{verseOptions.map(v => <option key={v}>{v}</option>)}</select></div>
            <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Vers. Fim</label><select disabled={!localData.bibleChapter || !localData.plusOneVerse} className="w-full border p-2 text-sm rounded-lg disabled:bg-slate-100 disabled:opacity-50 font-bold text-slate-700 bg-slate-50" value={localData.verseEnd} onChange={e=>setLocalData({...localData, verseEnd: e.target.value})}><option value="">Fim</option>{verseOptions.map(v => <option key={v}>{v}</option>)}</select></div>
            <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Leitor</label><input placeholder="Nome do irmão" className="w-full border p-2 text-sm rounded-lg font-bold text-slate-700 bg-slate-50" value={localData.readerName} onChange={e=>setLocalData({...localData,readerName:e.target.value})}/></div>
         </div>
      </div>
    </div>
  );
}

function FestivitiesView({ festivities, setFestivities, schedules, setSchedules, confirmAction, moveToTrash, viewMode }) {
  const [activeSubTab, setActiveSubTab] = useState('pendentes');
  const [showForm, setShowForm] = useState(false);
  const [selectedIds, setSelectedIds] = useState([]);
  const defaultForm = { id: '', category: 'Festividade', eventType: 'Campanha', congregacao: 'Cidade Garapu (Padrão)', anosCompleting: '', isJubilee: false, jubileeType: 'Papel', startDate: getTodayLocalStr(), endDate: getTodayLocalStr(), time: '', duration: '1', agendamentos: [], hasTema: false, temaBook: '', temaChapter: '', temaVerseStart: '', plusOneVerse: false, temaVerseEnd: '', hasSubtema: false, subtemaText: '', observacoes: '', requireLive: false, colors: [] };
  const [formData, setFormData] = useState(defaultForm);
  
  const isPendente = (f) => {
    const related = schedules.filter(s => s.festivityId === f.id);
    if(related.length > 0) return related.some(s => !s.completed);
    return f.endDate >= getTodayLocalStr();
  };

  const pending = festivities.filter(isPendente).sort((a,b) => new Date(a.startDate||0) - new Date(b.startDate||0));
  const finished = festivities.filter(f => !isPendente(f)).sort((a,b) => new Date(b.startDate||0) - new Date(a.startDate||0));
  const displayedList = activeSubTab === 'pendentes' ? pending : finished;
  const allVisibleIds = displayedList.map(f => f.id);

  const toggleSelection = (id) => setSelectedIds(prev => prev.includes(id) ? prev.filter(x => x !== id) : [...prev, id]);

  const handleBulkDelete = () => {
     selectedIds.forEach(id => {
        const f = festivities.find(x => x.id === id);
        if(f) {
           moveToTrash('Festividade', f);
           setSchedules(prev => prev.filter(s => s.festivityId !== id));
        }
     });
     setFestivities(festivities.filter(f => !selectedIds.includes(f.id)));
     setSelectedIds([]);
  };

  const handleStartDateChange = (e) => {
    const newStart = e.target.value;
    let newEnd = formData.endDate;
    if (newStart > newEnd) newEnd = newStart;
    const newDur = calculateDuration(newStart, newEnd);
    setFormData({...formData, startDate: newStart, endDate: newEnd, duration: newDur.toString()});
  };

  const handleEndDateChange = (e) => {
    const newEnd = e.target.value;
    let newStart = formData.startDate;
    if (newEnd < newStart) newStart = newEnd;
    const newDur = calculateDuration(newStart, newEnd);
    setFormData({...formData, startDate: newStart, endDate: newEnd, duration: newDur.toString()});
  };

  const handleDurationChange = (e) => {
    let val = parseInt(e.target.value, 10);
    if (isNaN(val) || val < 1) val = 1;
    const newEnd = calculateProtocolEndDate(formData.startDate, val - 1);
    setFormData({...formData, duration: val.toString(), endDate: newEnd});
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    confirmAction('Deseja salvar esta Festividade/Evento? O sistema irá gerar as escalas automaticamente com base nas datas e duração.', () => {
      const finalData = { ...formData };
      if (finalData.category === 'Festividade') finalData.congregacao = ''; 

      if (formData.id) {
         setFestivities(festivities.map(f => f.id === formData.id ? finalData : f));
         setSchedules(schedules.map(s => {
            if(s.festivityId === formData.id && !s.completed) {
               return {...s, liveAssignees: finalData.requireLive ? s.liveAssignees : []};
            }
            return s;
         }));
      } else {
         const newId = generateId();
         const newFest = { ...finalData, id: newId };
         setFestivities([...festivities, newFest]);
         
         const dur = parseInt(newFest.duration, 10);
         const newSchedules = [];
         for(let i=0; i<dur; i++) {
            let dayLabel = '';
            if (dur > 1) {
               if (i === 0) dayLabel = ' (Abertura)';
               else if (i === dur - 1) dayLabel = ' (Encerramento)';
               else dayLabel = ` (${i+1}º Dia)`;
            }
            const evtName = newFest.eventType === 'ProjeFérias' && newFest.projeFeriasTipo ? `ProjeFérias (${newFest.projeFeriasTipo})` : newFest.eventType;
            newSchedules.push({
               id: generateId(),
               festivityId: newId,
               category: newFest.category, 
               date: calculateProtocolEndDate(newFest.startDate, i),
               time: newFest.time,
               eventName: `${evtName}${dayLabel}`,
               assigned: [], support: [], supervisor: [], liveAssignees: [], completed: false
            });
         }
         setSchedules([...schedules, ...newSchedules]);
      }
      setShowForm(false);
    });
  };

  const deleteFestivity = (f) => { 
    confirmAction('Mover festividade para lixeira? Isso apagará também as escalas vinculadas.', () => { 
      moveToTrash('Festividade', f);
      setFestivities(festivities.filter(x => x.id !== f.id)); 
      setSchedules(schedules.filter(s => s.festivityId !== f.id));
      setSelectedIds(prev => prev.filter(id => id !== f.id));
    }); 
  };
  
  const [agendamentoModal, setAgendamentoModal] = useState({ isOpen: false, festId: null });
  const [agForm, setAgForm] = useState({ tipo: 'Gravação de Vídeo', date: '', time: '' });
  
  const addAgendamento = () => {
    const fId = agendamentoModal.festId;
    setFestivities(festivities.map(f => {
       if (f.id !== fId) return f;
       return { ...f, agendamentos: [...getArray(f.agendamentos), { ...agForm, id: generateId() }] };
    }));
    setAgendamentoModal({ isOpen: false, festId: null });
  };

  const addColor = () => { if(getArray(formData.colors).length < 4) setFormData({...formData, colors: [...getArray(formData.colors), '#3b82f6']})};
  const updateColor = (idx, val) => { const newColors = [...getArray(formData.colors)]; newColors[idx] = val; setFormData({...formData, colors: newColors})};
  const removeColor = (idx) => { const newColors = [...getArray(formData.colors)]; newColors.splice(idx, 1); setFormData({...formData, colors: newColors})};

  const optionsFest = ['Campanha', 'Círculo de Oração', 'Círculo de Oração Infantil', 'Conjunto Musical & Eletrônico', 'Coral', 'Escola Bíblica Animada', 'Grupo Jovem', 'PROATI', 'Templo', 'União de Adolescentes'].sort();
  const optionsEvento = ['COI Missionário', 'Conferência Missionária', 'Cruzada Evangelística', 'Culto de Missões', 'Culto em Ação de Graça', 'Culto Evangelístico', 'Culto Jovem Unificado', 'Culto para Casais', 'Desfile', 'Escola Bíblica de Férias', 'Estudo Bíblico', 'Evangelismo (Pernambuco para Cristo)', 'Evangelismo em Massa', 'Exposição Bíblica', 'Exposição Missionária', 'Mini-Vigília', 'Pré-Congresso de Adolescentes', 'Pré-Congresso de Jovens', 'Pré-Congresso Unificado', 'ProjeFérias', 'Seminário', 'Simpósio', 'Vigília'].sort();
  const optionsEncontro = ['Campanhas', 'Comissões', 'Conjuntos', 'Corais', 'Crianças', 'Grupos Jovens', 'Mocidade', 'Uniões'].sort();
  const congregacoes = ['Cidade Garapu (Padrão)', 'Garapu II', 'Garapu III', 'Vila Claudete', 'Vila Nova', 'Canaã', 'Vila Nova - Galileia', 'Porto Rico'];
  const projeFeriasTipos = ['Estudo (Or. da Mocidade)', 'Estudo (Domingo)', 'Gincana', 'Estudo (Feriado)', 'Dia Jovem', 'Evangelismo (Mocidade)', 'Encerramento (Premiação)', 'Batalha Bíblica', 'Vestibular Bíblico'];
  const jubileeOptions = ['Papel', 'Madeira', 'Estanho', 'Cristal', 'Porcelana', 'Prata', 'Pérola', 'Corais', 'Esmeralda', 'Rubi', 'Ouro', 'Ametista', 'Diamante', 'Safira', 'Vinho'];
  const bibleBooks = ['Gênesis', 'Êxodo', 'Levítico', 'Números', 'Deuteronômio', 'Josué', 'Juízes', 'Rute', '1 Samuel', '2 Samuel', '1 Reis', '2 Reis', '1 Crônicas', '2 Crônicas', 'Esdras', 'Neemias', 'Ester', 'Jó', 'Salmos', 'Provérbios', 'Eclesiastes', 'Cânticos', 'Isaías', 'Jeremias', 'Lamentações', 'Ezequiel', 'Daniel', 'Oséias', 'Joel', 'Amós', 'Obadias', 'Jonas', 'Miquéias', 'Naum', 'Habacuque', 'Sofonias', 'Ageu', 'Zacarias', 'Malaquias', 'Mateus', 'Marcos', 'Lucas', 'João', 'Atos', 'Romanos', '1 Coríntios', '2 Coríntios', 'Gálatas', 'Efésios', 'Filipenses', 'Colossenses', '1 Tessalonicenses', '2 Tessalonicenses', '1 Timóteo', '2 Timóteo', 'Tito', 'Filemom', 'Hebreus', 'Tiago', '1 Pedro', '2 Pedro', '1 João', '2 João', '3 João', 'Judas', 'Apocalipse'];
  const verseOptions = Array.from({length: 176}, (_, i) => i + 1);

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 lg:grid-cols-2 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-6">
      <div className="flex justify-between border-b pb-4">
        <h2 className="text-2xl font-bold">Festividades & Eventos</h2>
        <div className="flex gap-4">
          <div className="bg-slate-200 p-1 rounded-lg flex"><button onClick={()=>setActiveSubTab('pendentes')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='pendentes'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Pendentes</button><button onClick={()=>setActiveSubTab('finalizados')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='finalizados'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Finalizados</button></div>
          {!showForm && <button onClick={() => { setFormData(defaultForm); setShowForm(true); }} className="bg-blue-600 text-white px-4 py-1.5 rounded-lg font-bold flex items-center gap-1 shadow-md hover:bg-blue-700 transition-colors"><Plus size={16}/> Novo</button>}
        </div>
      </div>

      <SelectionBar selectedIds={selectedIds} allIds={allVisibleIds} onSelectAll={()=>setSelectedIds(allVisibleIds)} onDeselectAll={()=>setSelectedIds([])} onBulkDelete={handleBulkDelete} />

      {showForm && (
        <form onSubmit={handleSubmit} className="bg-white p-6 rounded-xl border grid grid-cols-1 sm:grid-cols-3 gap-4 shadow-sm relative">
           <div><label className="block text-sm mb-1 font-bold">Categoria</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.category} onChange={e=>setFormData({...formData, category: e.target.value, eventType: e.target.value==='Festividade'?optionsFest[0]:e.target.value==='Eventos'?optionsEvento[0]:optionsEncontro[0]})}><option>Festividade</option><option>Eventos</option><option>Encontro</option></select></div>
           
           <div className={formData.category==='Eventos' || formData.category==='Encontro' ? "col-span-1" : "col-span-1 sm:col-span-2"}><label className="block text-sm mb-1 font-bold">Festividade / Evento</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.eventType} onChange={e=>setFormData({...formData, eventType: e.target.value})}>{(formData.category==='Festividade'?optionsFest:formData.category==='Eventos'?optionsEvento:optionsEncontro).map(o=><option key={o}>{o}</option>)}</select></div>
           
           {(formData.category==='Eventos' || formData.category==='Encontro') && (
              <div><label className="block text-sm mb-1 font-bold flex items-center gap-1"><MapPin size={14} className="text-slate-400"/> Congregação</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.congregacao} onChange={e=>setFormData({...formData, congregacao: e.target.value})}>{congregacoes.map(c=><option key={c} value={c}>{c}</option>)}</select></div>
           )}

           {formData.category === 'Eventos' && formData.eventType === 'ProjeFérias' && (
              <div className="col-span-1 sm:col-span-3 p-3 bg-slate-50 border rounded-xl"><label className="block text-sm font-bold mb-1">Tipo de ProjeFérias</label><select required className="w-full p-2 border rounded font-bold text-slate-700 bg-white" value={formData.projeFeriasTipo} onChange={e=>setFormData({...formData, projeFeriasTipo: e.target.value})}><option value="">Selecione...</option>{projeFeriasTipos.map(o=><option key={o}>{o}</option>)}</select></div>
           )}
           
           {formData.category === 'Festividade' && (
             <div className="col-span-1 sm:col-span-3 grid grid-cols-1 sm:grid-cols-3 gap-4 border-t border-slate-100 pt-4 mt-2">
               <div><label className="block text-sm mb-1 font-bold">Anos Completando</label><input type="number" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.anosCompleting} onChange={e=>setFormData({...formData, anosCompleting: e.target.value})}/></div>
               <div className="flex items-center"><label className="flex items-center gap-2 sm:mt-4 font-bold cursor-pointer text-slate-700"><input type="checkbox" className="w-4 h-4 rounded text-blue-600" checked={formData.isJubilee} onChange={e=>setFormData({...formData, isJubilee: e.target.checked})}/> É Jubileu?</label></div>
               {formData.isJubilee && (
                  <div><label className="block text-sm mb-1 font-bold">Tipo de Jubileu</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.jubileeType} onChange={e=>setFormData({...formData, jubileeType: e.target.value})}>{jubileeOptions.map(j=><option key={j}>{j}</option>)}</select></div>
               )}
               <div className="col-span-1 sm:col-span-3 bg-red-50/50 p-3 rounded-lg border border-red-100">
                  <label className="flex items-center gap-2 font-bold text-red-800 cursor-pointer"><input type="checkbox" className="w-4 h-4 rounded text-red-600" checked={formData.requireLive} onChange={e=>setFormData({...formData, requireLive: e.target.checked})}/> <Video size={16}/> Requisição de Gravação de Live</label>
                  <p className="text-xs text-red-600/70 ml-6 mt-1 font-medium">Habilita a seleção de equipe para Live nas escalas vinculadas.</p>
               </div>
             </div>
           )}
           
           <div className="col-span-1 sm:col-start-1 mt-2"><label className="block text-sm mb-1 font-bold">Data de Início</label><input required type="date" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.startDate} onChange={handleStartDateChange}/></div>
           <div className="mt-2"><label className="block text-sm mb-1 font-bold">Data de Finalização</label><input required type="date" min={formData.startDate} disabled={!!formData.id} className="w-full p-2 border rounded disabled:opacity-50 disabled:bg-slate-100 font-bold text-slate-700 bg-slate-50" title={formData.id?"Data final não editável após criado":""} value={formData.endDate} onChange={handleEndDateChange}/></div>
           <div className="mt-2"><label className="block text-sm mb-1 font-bold">Duração (Dias)</label><input required type="number" min="1" disabled={!!formData.id} className="w-full p-2 border rounded disabled:opacity-50 disabled:bg-slate-100 font-bold text-slate-700 bg-slate-50" title={formData.id ? "A duração não pode ser alterada após a criação." : ""} value={formData.duration} onChange={handleDurationChange}/></div>
           <div className="mt-2"><label className="block text-sm mb-1 font-bold">Horário Padrão</label><input required type="time" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.time} onChange={e=>setFormData({...formData, time: e.target.value})}/></div>

           {/* Cores da Festividade */}
           {formData.category === 'Festividade' && (
              <div className="col-span-1 sm:col-span-2 sm:col-start-1 mt-2 bg-slate-50 p-3 rounded-lg border border-slate-200">
                 <div className="flex justify-between items-center mb-2">
                    <label className="text-sm font-bold flex items-center gap-1"><Palette size={16}/> Paleta de Cores</label>
                    <button type="button" onClick={addColor} disabled={getArray(formData.colors).length>=4} className="text-xs bg-blue-100 text-blue-700 font-bold px-2 py-1 rounded hover:bg-blue-200 disabled:opacity-50 transition-colors flex items-center gap-1"><Plus size={12}/> Adicionar</button>
                 </div>
                 {getArray(formData.colors).length === 0 ? <p className="text-xs text-slate-400 italic">Nenhuma cor definida.</p> : (
                    <div className="flex flex-wrap gap-3">
                       {formData.colors.map((c, idx) => (
                          <div key={idx} className="flex flex-col items-center gap-1 group">
                             <div className="relative w-10 h-10 rounded-full border shadow-sm overflow-hidden shrink-0 cursor-pointer">
                                <input type="color" value={c} onChange={e=>updateColor(idx, e.target.value)} className="absolute inset-0 w-[200%] h-[200%] -top-2 -left-2 cursor-pointer border-none p-0 outline-none" />
                             </div>
                             <div className="flex items-center gap-1 bg-white border rounded px-1 text-[10px] font-mono">
                                {c.toUpperCase()} <button type="button" onClick={()=>removeColor(idx)} className="text-red-500 hover:text-red-700"><X size={10}/></button>
                             </div>
                          </div>
                       ))}
                    </div>
                 )}
              </div>
           )}

           <div className="col-span-1 sm:col-span-3 border-t pt-4 mt-2">
              <label className="flex items-center gap-2 font-bold text-slate-800 mb-3"><input type="checkbox" className="w-4 h-4 rounded text-blue-600" checked={formData.hasTema} onChange={e=>setFormData({...formData, hasTema: e.target.checked})}/> Possui Tema?</label>
              {formData.hasTema && (
                 <div className="grid grid-cols-1 sm:grid-cols-4 gap-4 bg-slate-50 p-4 border rounded-xl mb-4">
                    <div className="col-span-1 sm:col-span-4 flex justify-between items-center border-b pb-1">
                       <h5 className="font-bold text-sm text-amber-600">Referência Bíblica</h5>
                       <label className="flex items-center gap-1.5 text-xs font-bold text-slate-600 cursor-pointer"><input type="checkbox" className="w-3.5 h-3.5 rounded text-blue-600" checked={formData.plusOneVerse} onChange={e=>setFormData({...formData, plusOneVerse: e.target.checked, temaVerseEnd: ''})}/> + 1 Versículo</label>
                    </div>
                    <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Livro</label><select className="w-full border p-2 text-sm rounded-lg font-bold text-slate-700 bg-white" value={formData.temaBook} onChange={e=>setFormData({...formData,temaBook:e.target.value, temaChapter:'', temaVerseStart: '', temaVerseEnd: ''})}><option value="">Livro</option>{bibleBooks.map(b=><option key={b}>{b}</option>)}</select></div>
                    <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Capítulo</label><select disabled={!formData.temaBook} className="w-full border p-2 text-sm rounded-lg disabled:bg-slate-100 font-bold text-slate-700 bg-white" value={formData.temaChapter} onChange={e=>setFormData({...formData,temaChapter:e.target.value})}><option value="">Cap.</option>{formData.temaBook && Array.from({length:300},(_,i)=><option key={i+1}>{i+1}</option>)}</select></div>
                    <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Vers. Início</label><select disabled={!formData.temaChapter} className="w-full border p-2 text-sm rounded-lg disabled:bg-slate-100 font-bold text-slate-700 bg-white" value={formData.temaVerseStart} onChange={e=>setFormData({...formData, temaVerseStart: e.target.value})}><option value="">Início</option>{verseOptions.map(v => <option key={v}>{v}</option>)}</select></div>
                    <div><label className="block text-[10px] uppercase tracking-wider font-bold text-slate-500 mb-1">Vers. Fim</label><select disabled={!formData.temaChapter || !formData.plusOneVerse} className="w-full border p-2 text-sm rounded-lg disabled:bg-slate-100 disabled:opacity-50 font-bold text-slate-700 bg-white" value={formData.temaVerseEnd} onChange={e=>setFormData({...formData, temaVerseEnd: e.target.value})}><option value="">Fim</option>{verseOptions.map(v => <option key={v}>{v}</option>)}</select></div>
                    
                    <div className="col-span-1 sm:col-span-4 mt-2">
                       <label className="flex items-center gap-2 font-bold text-slate-700 mb-2"><input type="checkbox" className="w-4 h-4 rounded text-blue-600" checked={formData.hasSubtema} onChange={e=>setFormData({...formData, hasSubtema: e.target.checked})}/> Adicionar Subtema?</label>
                       {formData.hasSubtema && <input placeholder="Descreva o Subtema" className="w-full border p-2 rounded-lg font-bold text-slate-700 bg-white" value={formData.subtemaText} onChange={e=>setFormData({...formData, subtemaText: e.target.value})} />}
                    </div>
                 </div>
              )}
           </div>

           <div className="col-span-1 sm:col-span-3">
              <label className="block text-sm font-bold mb-1">Observações Adicionais</label>
              <textarea className="w-full p-2 border rounded-lg bg-slate-50 text-slate-700" rows="3" value={formData.observacoes} onChange={e=>setFormData({...formData, observacoes: e.target.value})} placeholder="Insira detalhes gerais..."></textarea>
           </div>
           
           <div className="col-span-1 sm:col-span-3 text-right flex justify-end gap-2 mt-4 border-t pt-4">
             <button type="button" onClick={() => setShowForm(false)} className="px-6 py-2 border rounded-lg text-slate-600 font-bold hover:bg-slate-50 transition-colors">Cancelar</button>
             <button type="submit" className="bg-slate-900 text-white px-6 py-2 rounded-lg font-bold shadow-md hover:bg-slate-800 transition-colors">Salvar Evento</button>
           </div>
        </form>
      )}

      <div className={gridClass}>
        {displayedList.map(f => (
          <FestivityCard key={f.id} f={f} selectedIds={selectedIds} toggleSelection={toggleSelection} deleteFestivity={deleteFestivity} setFormData={setFormData} setShowForm={setShowForm} activeSubTab={activeSubTab} setAgForm={setAgForm} setAgendamentoModal={setAgendamentoModal} festivities={festivities} setFestivities={setFestivities} confirmAction={confirmAction} />
        ))}
        {displayedList.length === 0 && !showForm && <div className="col-span-full py-12 text-center text-slate-500">Nenhum evento encontrado.</div>}
      </div>

      {agendamentoModal.isOpen && (
         <div className="fixed inset-0 bg-slate-900/60 flex items-center justify-center p-4 z-50">
            <div className="bg-white p-6 rounded-xl w-full max-w-sm shadow-2xl">
               <h3 className="font-bold text-lg mb-4">Novo Agendamento</h3>
               <div className="space-y-4">
                  <div><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={agForm.tipo} onChange={e=>setAgForm({...agForm, tipo:e.target.value})}><option>Gravação de Vídeo</option><option>Fotos do Órgão</option><option>Gravação de Testemunhos</option></select></div>
                  <div><input type="date" required className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={agForm.date} onChange={e=>setAgForm({...agForm, date:e.target.value})}/></div>
                  <div><input type="time" required className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={agForm.time} onChange={e=>setAgForm({...agForm, time:e.target.value})}/></div>
               </div>
               <div className="flex justify-end gap-2 mt-6">
                  <button onClick={()=>setAgendamentoModal({isOpen:false, festId:null})} className="px-4 py-2 border rounded font-bold text-slate-600 hover:bg-slate-50 transition-colors">Cancelar</button>
                  <button onClick={addAgendamento} className="px-4 py-2 bg-blue-600 text-white rounded font-bold hover:bg-blue-700 transition-colors">Agendar</button>
               </div>
            </div>
         </div>
      )}
    </div>
  );
}

function FestivityCard({ f, selectedIds, toggleSelection, deleteFestivity, setFormData, setShowForm, activeSubTab, setAgForm, setAgendamentoModal, festivities, setFestivities, confirmAction }) {
   const verseText = useBibleVerse(f.temaBook, f.temaChapter, f.temaVerseStart, f.temaVerseEnd);
   const [showDetails, setShowDetails] = useState(false);
   const isSelected = selectedIds.includes(f.id);
   const fNameStr = f.eventType === 'ProjeFérias' && f.projeFeriasTipo ? `ProjeFérias (${f.projeFeriasTipo})` : f.eventType;
   
   const eventCodes = { 'Doutrina': '04', 'Santa Ceia': '03', 'Culto de Domingo': '02', 'Culto da Mocidade': '10', 'Culto do PROATI': '58', 'Culto da Família': '02', 'Culto Infantil': '02', 'Consagração': '05', 'Conjunto Musical & Eletrônico': '43', 'Coral': '47', 'União de Adolescentes': '25', 'PROATI': '62', 'Templo': '16', 'Círculo de Oração Infantil': '09', 'Grupo Jovem': '44', 'Círculo de Oração': '15', 'Campanha': '07', 'Escola Bíblica Animada': '36', 'Simpósio': '57', 'Pré-Congresso de Jovens': '29', 'Pré-Congresso de Adolescentes': '29', 'Pré-Congresso Unificado': '29', 'Culto Jovem Unificado': '31', 'Estudo Bíblico': '20', 'Culto para Casais': '18', 'Evangelismo em Massa': '27', 'Evangelismo (Pernambuco para Cristo)': '27', 'Culto de Missões': '17', 'Conferência Missionária': '30', 'Culto Evangelístico': '14', 'Escola Bíblica de Férias': '54', 'Desfile': '01', 'Cruzada Evangelística': '37', 'Seminário': '23', 'COI Missionário': '18', 'Exposição Bíblica': '68', 'Exposição Missionária': '68', 'Vigília': '42', 'Mini-Vigília': '41', 'ProjeFérias': '12', 'Crianças': '50'};
   const baseName = fNameStr.split(' (')[0];
   const code = eventCodes[baseName];
   const fullTitle = `${f.category}: ${fNameStr}${code ? ` (CÓD. ${code})` : ''}`;

   return (
      <>
         <div className={`bg-white p-5 rounded-xl border flex flex-col gap-4 shadow-sm hover:shadow-md transition-all relative h-full ${isSelected ? 'border-blue-500 ring-2 ring-blue-200' : 'border-slate-200'}`}>
            <input type="checkbox" className="absolute top-4 right-4 z-10 w-4 h-4 cursor-pointer text-blue-600 rounded" checked={isSelected} onChange={(e)=>{e.stopPropagation(); toggleSelection(f.id);}} />
            <div className="flex justify-between items-start gap-4 pr-6 cursor-pointer" onClick={()=>toggleSelection(f.id)}>
               <div className="flex gap-4 items-start min-w-0 flex-1">
                  <div className="bg-amber-50 text-amber-600 p-2 rounded-lg text-center w-16 shrink-0 flex flex-col justify-center items-center border border-amber-100">
                     <span className="text-[10px] font-bold uppercase tracking-wider mb-0.5">{getWeekdayShort(f.startDate)}</span>
                     <span className="font-black text-2xl leading-none">{f.startDate.split('-')[2]}</span>
                     <span className="text-[10px] font-bold uppercase mt-0.5">{getMonthName(f.startDate).substring(0,3)}</span>
                  </div>
                  <div className="flex-1 min-w-0 break-words flex flex-col justify-center">
                    <h3 className="font-bold text-lg leading-tight text-slate-800 whitespace-normal mb-1" title={fullTitle}>{fullTitle}</h3>
                    <div className="text-[10px] font-bold uppercase tracking-wider text-slate-500 mb-1 flex items-center gap-1">
                       <Clock size={12}/> {f.time || 'Não definido'}
                    </div>
                    <div className="flex flex-wrap gap-2 items-center mt-1">
                       {f.congregacao && <span className="text-[10px] bg-slate-100 text-slate-700 border border-slate-200 px-2 py-0.5 rounded font-bold uppercase tracking-wide flex items-center gap-1"><MapPin size={10}/> {f.congregacao}</span>}
                       {f.category === 'Festividade' && f.anosCompleting && <span className="text-[10px] bg-amber-100 text-amber-800 px-2 py-0.5 rounded font-bold uppercase tracking-wide whitespace-normal leading-tight">{f.anosCompleting} Anos {f.isJubilee && f.jubileeType ? `(Jubileu de ${f.jubileeType})` : ''}</span>}
                       {f.requireLive && <span className="text-[10px] bg-red-100 text-red-800 px-2 py-0.5 rounded font-bold uppercase tracking-wide flex items-center gap-1 border border-red-200"><Video size={10}/> Live</span>}
                    </div>
                  </div>
               </div>
            </div>
            
            <div className="grid grid-cols-1 sm:grid-cols-3 gap-2 text-sm bg-slate-50 p-3 rounded-lg border border-slate-100 mt-auto">
               <div className="text-left sm:text-center p-1"><span className="block text-[10px] text-slate-400 font-bold uppercase tracking-wider mb-0.5">Início</span><span className="font-semibold text-slate-700 whitespace-normal leading-tight">{getWeekdayShort(f.startDate)}, {formatDateBR(f.startDate)}</span></div>
               <div className="text-left sm:text-center sm:border-l border-slate-200 p-1"><span className="block text-[10px] text-slate-400 font-bold uppercase tracking-wider mb-0.5">Fim</span><span className="font-semibold text-slate-700 whitespace-normal leading-tight">{f.endDate > f.startDate ? `${getWeekdayShort(f.endDate)}, ${formatDateBR(f.endDate)}` : '-'}</span></div>
               <div className="text-left sm:text-center sm:border-l border-slate-200 p-1"><span className="block text-[10px] text-slate-400 font-bold uppercase tracking-wider mb-0.5">Duração</span><span className="font-semibold text-slate-700">{f.duration} Dias</span></div>
            </div>
            
            <div className="flex gap-2 flex-wrap w-full shrink-0">
               <button onClick={(e)=>{e.stopPropagation(); setShowDetails(true);}} className="flex-1 py-2 text-blue-600 font-bold text-xs bg-blue-50 hover:bg-blue-100 border border-blue-200 rounded-lg transition-colors flex items-center justify-center gap-1"><Info size={14}/> Detalhes</button>
               <button onClick={()=>{setFormData(f);setShowForm(true);}} className="flex-1 py-2 text-slate-600 font-bold text-xs bg-slate-50 hover:bg-slate-100 border rounded-lg transition-colors flex items-center justify-center gap-1"><Edit size={14}/> Editar</button>
               <button onClick={()=>deleteFestivity(f)} className="flex-1 py-2 text-red-500 font-bold text-xs bg-white hover:bg-red-50 border rounded-lg transition-colors flex items-center justify-center gap-1"><Trash2 size={14}/> Excluir</button>
            </div>
         </div>

         {showDetails && (
            <div className="fixed inset-0 bg-slate-900/60 flex items-center justify-center p-4 z-[100] backdrop-blur-sm">
               <div className="bg-white p-6 rounded-xl w-full max-w-xl relative shadow-2xl max-h-[90vh] flex flex-col">
                  <div className="flex justify-between items-center border-b pb-3 mb-4 shrink-0">
                     <h3 className="font-bold text-xl text-slate-800 flex items-center gap-2"><Info size={20} className="text-blue-500"/> Detalhes do Evento</h3>
                     <button onClick={()=>setShowDetails(false)} className="p-1.5 hover:bg-slate-100 rounded-lg text-slate-500 transition-colors"><X size={20}/></button>
                  </div>
                  
                  <div className="overflow-y-auto space-y-6 flex-1 pr-2">
                     {(f.congregacao || getArray(f.colors).length > 0) && (
                        <div className="bg-slate-50 p-4 border rounded-xl flex flex-col sm:flex-row gap-4 justify-between items-start">
                           {f.congregacao && (
                              <div>
                                 <span className="text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1 flex items-center gap-1"><MapPin size={12}/> Congregação</span>
                                 <span className="font-bold text-slate-700">{f.congregacao}</span>
                              </div>
                           )}
                           {getArray(f.colors).length > 0 && (
                              <div>
                                 <span className="text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1 flex items-center gap-1"><Palette size={12}/> Paleta de Cores</span>
                                 <div className="flex -space-x-1">
                                    {f.colors.map((c, i) => (
                                       <div key={i} className="w-6 h-6 rounded-full border-2 border-white shadow-sm" style={{backgroundColor: c}} title={c}></div>
                                    ))}
                                 </div>
                              </div>
                           )}
                        </div>
                     )}

                     {f.hasTema && (
                       <div className="bg-amber-50 border border-amber-100 p-4 rounded-xl text-sm flex flex-col relative shadow-sm">
                          <div className="font-bold text-amber-700 text-sm mb-1 flex items-center gap-1 border-b border-amber-200 pb-2"><BookOpen size={14}/> Tema Bíblico Oficial</div>
                          <div className="text-slate-800 font-bold mt-2 text-base">{f.temaBook} {f.temaChapter}{f.temaVerseStart ? `:${f.temaVerseStart}${f.temaVerseEnd && f.temaVerseEnd > f.temaVerseStart ? `-${f.temaVerseEnd}` : ''}` : ''}</div>
                          {f.hasSubtema && f.subtemaText && <div className="mt-1 text-amber-900 text-sm font-bold leading-tight uppercase tracking-wider">{f.subtemaText}</div>}
                          {verseText && (
                             <div className="mt-3 text-slate-700 text-sm italic bg-white p-3 rounded border border-amber-100 shadow-inner relative pr-8">
                                "{verseText}"
                                <button onClick={(e)=>{e.stopPropagation(); navigator.clipboard.writeText(verseText).catch(()=>{});}} className="absolute top-2 right-2 p-1.5 text-amber-600 hover:bg-amber-100 rounded transition-colors" title="Copiar Texto Bíblico"><Copy size={14}/></button>
                             </div>
                          )}
                       </div>
                     )}

                     {f.observacoes && (
                       <div className="bg-slate-50 p-4 rounded-xl border text-sm text-slate-700 whitespace-pre-wrap break-words shadow-sm">
                          <b className="text-[10px] uppercase tracking-wider block mb-2 text-slate-500 border-b pb-1">Observações Gerais</b>
                          {f.observacoes}
                       </div>
                     )}

                     <div className="border-t pt-4">
                        <div className="flex justify-between items-center mb-3">
                           <h4 className="text-sm font-bold text-slate-800 uppercase tracking-wider flex items-center gap-2"><Video size={16}/> Agendamentos Extras</h4>
                           {activeSubTab === 'pendentes' && <button onClick={()=>{setAgForm({tipo:'Gravação de Vídeo', date:'', time:''});setAgendamentoModal({isOpen:true, festId: f.id});}} className="text-xs bg-blue-100 text-blue-800 hover:bg-blue-200 px-3 py-1.5 rounded-lg font-bold flex items-center gap-1 transition-colors"><Plus size={14}/> Agendar</button>}
                        </div>
                        {getArray(f.agendamentos).length === 0 ? <p className="text-sm text-slate-500 italic bg-slate-50 p-4 rounded-xl border text-center">Nenhum agendamento extra registrado.</p> : (
                           <ul className="space-y-2">
                              {f.agendamentos.map(a => (
                                 <li key={a.id} className="bg-white p-3 rounded-xl border flex justify-between items-center shadow-sm">
                                    <div className="text-sm break-words min-w-0 pr-2">
                                       <strong className="text-slate-800 block mb-1 whitespace-normal">{a.tipo}</strong>
                                       <span className="text-slate-500 flex items-center gap-2 font-medium flex-wrap"><CalendarDays size={14}/> {getWeekdayShort(a.date)}, {formatDateBR(a.date)} <Clock size={14} className="ml-2"/> {a.time}</span>
                                    </div>
                                    {activeSubTab === 'pendentes' && <button onClick={()=>confirmAction('Excluir agendamento?', () => setFestivities(festivities.map(ff => ff.id===f.id ? {...ff, agendamentos: ff.agendamentos.filter(aa=>aa.id!==a.id)} : ff)))} className="text-slate-400 hover:text-red-500 hover:bg-red-50 p-2 rounded-lg transition-colors shrink-0"><Trash2 size={18}/></button>}
                                 </li>
                              ))}
                           </ul>
                        )}
                     </div>
                  </div>
               </div>
            </div>
         )}
      </>
   );
}

function MeetingsView({ members, exMemberIds, meetings, setMeetings, meetingAttendances, setMeetingAttendances, confirmAction, moveToTrash, viewMode, setPinModal }) {
  const [activeSubTab, setActiveSubTab] = useState('pendentes');
  const [showForm, setShowForm] = useState(false);
  const [expandedPanel, setExpandedPanel] = useState({ id: null, type: null }); 
  const [formData, setFormData] = useState({ id: '', subjectType: 'Reunião Mensal Normativa', customTitle: '', date: '', time: '', format: 'Presencial', durationHours: '1', durationMinutes: '00' });
  const [selectedIds, setSelectedIds] = useState([]);
  
  const subjects = ['Reunião de Abertura Anual', 'Reunião Mensal Normativa', 'Reunião para Festividade', 'Reunião para Evento da Área', 'Reunião de Fechamento Anual', 'Reunião de Confraternização', 'Outros'];
  const activeMembers = sortMembers(members.filter(m => !exMemberIds.includes(m.id)));

  const pending = meetings.filter(m => !m.completed).sort((a,b) => new Date(a.date||0) - new Date(b.date||0));
  const finished = meetings.filter(m => m.completed).sort((a,b) => new Date(b.date||0) - new Date(a.date||0));
  const displayedList = activeSubTab === 'pendentes' ? pending : finished;
  const allVisibleIds = displayedList.map(m => m.id);

  const toggleSelection = (id) => setSelectedIds(prev => prev.includes(id) ? prev.filter(x => x !== id) : [...prev, id]);

  const handleBulkDelete = () => {
     selectedIds.forEach(id => {
        const m = meetings.find(x => x.id === id);
        if(m) moveToTrash('Reunião', m);
     });
     setMeetings(meetings.filter(m => !selectedIds.includes(m.id)));
     setSelectedIds([]);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    confirmAction('Deseja salvar a reunião?', () => {
      const finalTitle = formData.subjectType === 'Outros' ? formData.customTitle : formData.subjectType;
      if (formData.id) setMeetings(meetings.map(m => m.id === formData.id ? { ...formData, title: finalTitle } : m));
      else setMeetings([...meetings, { ...formData, id: generateId(), title: finalTitle, completed: false }]);
      setShowForm(false);
    });
  };

  const handleAttChange = (meetId, memberId, field, val) => {
    const meetData = meetingAttendances[meetId] || {};
    const rec = meetData[memberId] || { status: 'Pendente', justification: '', rsvp: false };
    setMeetingAttendances({...meetingAttendances, [meetId]: {...meetData, [memberId]: {...rec, [field]: val}}});
  };

  const checkRsvpLock = (m) => {
     if(!m.date || !m.time) return false;
     const meetTime = new Date(`${m.date}T${m.time}`);
     const now = new Date();
     const diffMins = (meetTime - now) / (1000 * 60);
     return diffMins < 15; 
  };

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 md:grid-cols-2 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-6">
      <div className="flex justify-between border-b pb-4">
        <h2 className="text-2xl font-bold">Reuniões de Equipe</h2>
        <div className="flex gap-4">
          <div className="bg-slate-200 p-1 rounded-lg flex"><button onClick={()=>setActiveSubTab('pendentes')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='pendentes'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Pendentes</button><button onClick={()=>setActiveSubTab('concluidas')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='concluidas'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Concluídas</button></div>
          {!showForm && <button onClick={() => { setFormData({ id: '', subjectType: 'Reunião Mensal Normativa', customTitle: '', date: '', time: '', format: 'Presencial', durationHours: '1', durationMinutes: '00' }); setShowForm(true); }} className="bg-blue-600 text-white px-4 py-1.5 rounded-lg font-bold flex items-center gap-1 shadow-md hover:bg-blue-700 transition-colors"><Plus size={16}/> Agendar</button>}
        </div>
      </div>

      <SelectionBar selectedIds={selectedIds} allIds={allVisibleIds} onSelectAll={()=>setSelectedIds(allVisibleIds)} onDeselectAll={()=>setSelectedIds([])} onBulkDelete={handleBulkDelete} />

      {showForm && (
        <form onSubmit={handleSubmit} className="bg-white p-6 rounded-xl border grid grid-cols-1 sm:grid-cols-2 gap-4 shadow-sm">
           <div className="col-span-1 sm:col-span-2"><label className="block text-sm mb-1 font-bold">Pauta/Assunto</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.subjectType} onChange={e=>setFormData({...formData, subjectType: e.target.value})}>{subjects.map(s=><option key={s}>{s}</option>)}</select></div>
           {formData.subjectType === 'Outros' && <div className="col-span-1 sm:col-span-2"><input required placeholder="Descreva o assunto" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.customTitle} onChange={e=>setFormData({...formData, customTitle: e.target.value})}/></div>}
           <div><label className="block text-sm mb-1 font-bold">Data</label><input required type="date" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.date} onChange={e=>setFormData({...formData, date: e.target.value})}/></div>
           <div><label className="block text-sm mb-1 font-bold">Horário</label><input required type="time" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.time} onChange={e=>setFormData({...formData, time: e.target.value})}/></div>
           
           <div><label className="block text-sm mb-1 font-bold">Formato</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.format} onChange={e=>setFormData({...formData, format: e.target.value})}><option>Presencial</option><option>Online</option><option>Híbrida</option></select></div>
           <div>
              <label className="block text-sm mb-1 font-bold">Duração Estimada</label>
              <div className="flex gap-2">
                 <div className="flex-1 flex items-center bg-slate-50 border rounded pr-2"><input required type="number" min="0" max="10" className="w-full p-2 bg-transparent outline-none font-bold text-slate-700 text-right" value={formData.durationHours} onChange={e=>setFormData({...formData, durationHours: e.target.value})}/><span className="text-xs font-bold text-slate-400">h</span></div>
                 <div className="flex-1 flex items-center bg-slate-50 border rounded pr-2"><input required type="number" min="0" max="59" className="w-full p-2 bg-transparent outline-none font-bold text-slate-700 text-right" value={formData.durationMinutes} onChange={e=>setFormData({...formData, durationMinutes: e.target.value})}/><span className="text-xs font-bold text-slate-400">m</span></div>
              </div>
           </div>

           <div className="col-span-1 sm:col-span-2 text-right flex justify-end gap-2 mt-4 border-t pt-4">
             <button type="button" onClick={() => setShowForm(false)} className="px-6 py-2 border rounded-lg text-slate-600 font-bold hover:bg-slate-50 transition-colors">Cancelar</button>
             <button type="submit" className="bg-slate-900 text-white px-6 py-2 rounded-lg font-bold shadow-md hover:bg-slate-800 transition-colors">Salvar</button>
           </div>
        </form>
      )}

      <div className={gridClass}>
        {displayedList.map(m => {
           const rsvpLocked = activeSubTab === 'pendentes' ? checkRsvpLock(m) : true;
           
           return (
             <div key={m.id} className={`bg-white rounded-xl border overflow-hidden shadow-sm hover:shadow-md transition-all flex flex-col h-full relative ${selectedIds.includes(m.id) ? 'border-blue-500 ring-2 ring-blue-200' : 'border-slate-200'}`}>
                <input type="checkbox" className="absolute top-4 right-4 z-10 w-4 h-4 cursor-pointer text-blue-600 rounded" checked={selectedIds.includes(m.id)} onChange={(e)=>{e.stopPropagation(); toggleSelection(m.id);}} />
                <div className="p-4 flex gap-4 items-start flex-1 cursor-pointer pr-8" onClick={()=>toggleSelection(m.id)}>
                   <div className="bg-indigo-50 text-indigo-600 p-2 rounded-lg text-center w-16 shrink-0 flex flex-col justify-center items-center border border-indigo-100">
                      <span className="text-[10px] font-bold uppercase tracking-wider mb-0.5">{getWeekdayShort(m.date)}</span>
                      <span className="font-black text-2xl leading-none">{m.date.split('-')[2]}</span>
                      <span className="text-[10px] font-bold uppercase mt-0.5">{getMonthName(m.date).substring(0,3)}</span>
                   </div>
                   <div className="flex-1 min-w-0 break-words flex flex-col justify-center h-full">
                      <h3 className="font-bold text-lg text-slate-800 whitespace-normal mb-2 leading-tight">{m.title}</h3>
                      <div className="flex flex-wrap items-center gap-x-4 gap-y-2 mt-auto">
                         <p className="text-sm font-medium text-slate-500 flex items-center gap-1"><Clock size={14}/> {m.time}</p>
                         <p className="text-[10px] font-bold uppercase tracking-wider text-indigo-600 bg-indigo-50 border border-indigo-100 px-2 py-0.5 rounded">{m.format}</p>
                         <p className="text-[10px] font-bold text-slate-500 uppercase tracking-wider">Dur: {m.durationHours}h {m.durationMinutes}m</p>
                      </div>
                   </div>
                </div>
                <div className="p-4 bg-slate-50 border-t flex gap-2 shrink-0 flex-wrap justify-between mt-auto">
                   <button onClick={()=>setExpandedPanel(expandedPanel.id===m.id && expandedPanel.type==='rsvp' ? {id:null,type:null} : {id:m.id,type:'rsvp'})} className={`flex-1 px-3 py-2 border rounded-lg text-sm font-bold transition-colors flex justify-center items-center ${expandedPanel.id===m.id && expandedPanel.type==='rsvp'?'bg-blue-100 text-blue-800 border-blue-200':'bg-white hover:bg-slate-100 text-slate-700'}`}>RSVP</button>
                   <button onClick={()=>setExpandedPanel(expandedPanel.id===m.id && expandedPanel.type==='chamada' ? {id:null,type:null} : {id:m.id,type:'chamada'})} className={`flex-1 px-3 py-2 border rounded-lg text-sm font-bold transition-colors flex justify-center items-center ${expandedPanel.id===m.id && expandedPanel.type==='chamada'?'bg-emerald-100 text-emerald-800 border-emerald-200':'bg-white hover:bg-slate-100 text-slate-700'}`}>Chamada</button>
                   {activeSubTab === 'pendentes' && <button onClick={()=>confirmAction('Concluir reunião e enviar para o histórico?', ()=>setMeetings(meetings.map(x=>x.id===m.id?{...x,completed:true}:x)))} className="px-4 py-2 bg-emerald-50 text-emerald-600 border border-emerald-200 rounded-lg font-bold text-sm hover:bg-emerald-100 flex justify-center items-center transition-colors">Concluir</button>}
                   <button onClick={()=>confirmAction('Remover para a lixeira?', ()=> {moveToTrash('Reunião', m); setMeetings(meetings.filter(x=>x.id!==m.id));})} className="p-2 border rounded-lg text-slate-400 bg-white hover:text-red-500 hover:bg-red-50 flex justify-center items-center transition-colors"><Trash2 size={16} /></button>
                </div>
                
                {/* Painel RSVP */}
                {expandedPanel.id === m.id && expandedPanel.type === 'rsvp' && (
                  <div className="bg-white p-4 border-t border-blue-100">
                     <div className="flex justify-between items-center mb-3">
                        <h4 className="font-bold flex items-center gap-2 text-slate-800 text-sm"><UserCheck size={16}/> Confirmação Prévia (RSVP)</h4>
                        {activeSubTab === 'pendentes' && rsvpLocked && (
                           <button onClick={()=>setPinModal({isOpen:true, action: () => {
                              setMeetingAttendances({...meetingAttendances, [m.id]: {...meetingAttendances[m.id], _unlocked: true}});
                           }})} className="text-xs flex items-center gap-1 font-bold bg-amber-50 text-amber-700 border border-amber-200 px-2 py-1 rounded hover:bg-amber-100 transition-colors"><Lock size={12}/> Desbloquear RSVP</button>
                        )}
                     </div>
                     {rsvpLocked && !(meetingAttendances[m.id] && meetingAttendances[m.id]._unlocked) && activeSubTab === 'pendentes' && (
                        <div className="bg-red-50 border border-red-100 text-red-600 text-xs font-bold p-2 rounded mb-3 flex items-center gap-2"><Lock size={14}/> Fechado para confirmação (Tempo limite excedido).</div>
                     )}
                     <div className="max-h-64 overflow-y-auto bg-white border rounded-xl shadow-sm">
                        <table className="w-full text-left text-xs sm:text-sm"><thead className="bg-slate-100 border-b"><tr><th className="p-2 sm:p-3">Nome</th><th className="p-2 sm:p-3 text-center">RSVP (Vai?)</th></tr></thead>
                        <tbody>
                           {activeMembers.map(member => {
                              const rec = meetingAttendances[m.id]?.[member.id] || { status: 'Pendente', justification: '', rsvp: false };
                              const isLocked = activeSubTab==='concluidas' || (rsvpLocked && !(meetingAttendances[m.id] && meetingAttendances[m.id]._unlocked));
                              return (
                                <tr key={member.id} className="border-b last:border-0 hover:bg-slate-50 transition-colors">
                                   <td className="p-2 sm:p-3 font-bold text-slate-800 whitespace-normal break-words">{member.usualName || member.name}</td>
                                   <td className="p-2 sm:p-3 text-center">
                                      <input type="checkbox" disabled={isLocked} checked={rec.rsvp} onChange={e=>handleAttChange(m.id, member.id, 'rsvp', e.target.checked)} className="w-4 h-4 rounded text-blue-600 cursor-pointer disabled:opacity-50 disabled:cursor-not-allowed"/>
                                   </td>
                                </tr>
                              );
                           })}
                        </tbody></table>
                     </div>
                  </div>
                )}

                {/* Painel Chamada Oficial */}
                {expandedPanel.id === m.id && expandedPanel.type === 'chamada' && (
                  <div className="bg-white p-4 border-t border-emerald-100">
                     <h4 className="font-bold flex items-center gap-2 text-slate-800 text-sm mb-3"><ClipboardCheck size={16}/> Lista de Chamada Oficial</h4>
                     <div className="max-h-64 overflow-y-auto bg-white border rounded-xl shadow-sm">
                        <table className="w-full text-left text-xs sm:text-sm"><thead className="bg-slate-100 border-b"><tr><th className="p-2 sm:p-3">Nome</th><th className="p-2 sm:p-3 w-32 sm:w-40">Status</th><th className="p-2 sm:p-3 hidden sm:table-cell">Justificativa</th></tr></thead>
                        <tbody>
                           {activeMembers.map(member => {
                              const rec = meetingAttendances[m.id]?.[member.id] || { status: 'Pendente', justification: '', rsvp: false };
                              return (
                                <tr key={member.id} className="border-b last:border-0 hover:bg-slate-50 transition-colors">
                                   <td className="p-2 sm:p-3 font-bold text-slate-800 whitespace-normal break-words">
                                      {member.usualName || member.name}
                                      {rec.rsvp && <span className="ml-2 inline-block px-1.5 py-0.5 bg-blue-100 text-blue-700 text-[10px] rounded uppercase font-bold">Confirmou</span>}
                                   </td>
                                   <td className="p-2 sm:p-3"><select disabled={activeSubTab==='concluidas'} className="w-full border border-slate-300 rounded p-1 sm:p-1.5 font-bold text-slate-600 bg-white disabled:bg-slate-100 text-xs sm:text-sm" value={rec.status} onChange={e=>handleAttChange(m.id, member.id, 'status', e.target.value)}><option>Pendente</option><option>Presente</option><option>Ausente</option><option>Justificado</option></select></td>
                                   <td className="p-2 sm:p-3 hidden sm:table-cell"><input disabled={activeSubTab==='concluidas'||rec.status!=='Justificado'} placeholder="Apenas se justificado" className="w-full border border-slate-300 rounded p-1.5 disabled:opacity-50 disabled:bg-slate-100 text-xs sm:text-sm" value={rec.justification} onChange={e=>handleAttChange(m.id, member.id, 'justification', e.target.value)} /></td>
                                </tr>
                              );
                           })}
                        </tbody></table>
                     </div>
                  </div>
                )}
             </div>
           )
        })}
        {displayedList.length === 0 && !showForm && <div className="col-span-full py-12 text-center text-slate-500">Nenhuma reunião encontrada.</div>}
      </div>
    </div>
  );
}

function ProtocolsView({ members, exMemberIds, protocols, setProtocols, confirmAction, moveToTrash, viewMode, setPinModal }) {
  const [activeSubTab, setActiveSubTab] = useState('andamento');
  const [showForm, setShowForm] = useState(false);
  const [selectedIds, setSelectedIds] = useState([]);

  const [formData, setFormData] = useState({ id: '', memberId: '', targetMemberType: 'Efetivo', type: 'Disciplinar', subType: 'Advertência', infraction: 'Insubordinação Contínua', startDate: '', duration: '', notes: '', targetProtocolId: '' });

  const activeMembersFiltered = sortMembers(members.filter(m => !exMemberIds.includes(m.id) && m.type === formData.targetMemberType));

  const infraList = ['Insubordinação Contínua', 'Descumprimento de Funções', 'Ofensa e/ou agressão a outro membro da equipe', 'Medida disciplinar (referente à membresia da igreja)', 'Descumprimento de Escala'];

  const isInProgress = (p) => {
    if (p.isConcludedForced) return false;
    if (!['Suspensão de Nível 1', 'Suspensão de Nível 2', 'Afastamento Disciplinar', 'Solicitação de Afastamento'].includes(p.subType)) return false;
    if (!p.endDate) return true;
    return p.endDate >= getTodayLocalStr();
  };

  const andamento = protocols.filter(isInProgress).sort((a,b) => new Date(a.startDate||0) - new Date(b.startDate||0));
  const concluidosTotal = protocols.filter(p => !isInProgress(p)).sort((a,b) => new Date(b.endDate||b.startDate||0) - new Date(a.endDate||a.startDate||0));

  const concluidosDisc = concluidosTotal.filter(p => p.type === 'Disciplinar');
  const concluidosSoli = concluidosTotal.filter(p => p.type === 'Solicitação');

  const [concluidosTab, setConcluidosTab] = useState('disciplinares');

  const displayedList = activeSubTab === 'andamento' ? andamento : (concluidosTab === 'disciplinares' ? concluidosDisc : concluidosSoli);
  const allVisibleIds = displayedList.map(p => p.id);

  const toggleSelection = (id) => setSelectedIds(prev => prev.includes(id) ? prev.filter(x => x !== id) : [...prev, id]);

  const handleBulkDelete = () => {
     selectedIds.forEach(id => {
        const p = protocols.find(x => x.id === id);
        if(p) moveToTrash('Protocolo', p);
     });
     setProtocols(protocols.filter(p => !selectedIds.includes(p.id)));
     setSelectedIds([]);
  };

  const isDurational = ['Suspensão de Nível 1', 'Suspensão de Nível 2', 'Afastamento Disciplinar', 'Solicitação de Afastamento'].includes(formData.subType);
  const isConclusiva = formData.subType === 'Solicitação Conclusiva';

  const availableTargetProtocols = useMemo(() => {
     if (!formData.memberId) return [];
     return andamento.filter(p => p.memberId === formData.memberId);
  }, [formData.memberId, andamento]);

  const selectedTargetProtocol = useMemo(() => {
     if(!formData.targetProtocolId) return null;
     return protocols.find(p => p.id === formData.targetProtocolId);
  }, [formData.targetProtocolId, protocols]);

  const autoCalculatedEndDate = useMemo(() => {
     if (isDurational && formData.startDate && formData.duration) {
         return calculateProtocolEndDate(formData.startDate, parseInt(formData.duration) - 1);
     }
     return '';
  }, [isDurational, formData.startDate, formData.duration]);

  const generateProtocolNumber = (type, subType, dateStr) => {
      const typeMap = { 'Disciplinar': 1, 'Solicitação': 2 };
      const subTypeMap = {
          'Advertência': 1, 'Suspensão de Nível 1': 2, 'Suspensão de Nível 2': 3, 'Afastamento Disciplinar': 5, 'Exclusão': 4,
          'Solicitação de Afastamento': 1, 'Solicitação de Desligamento': 2, 'Solicitação Conclusiva': 3
      };
      const t = typeMap[type] || 0;
      const st = subTypeMap[subType] || 0;
      const d = dateStr ? new Date(dateStr + 'T00:00:00') : new Date();
      const month = String(d.getMonth() + 1).padStart(2, '0');
      const year = d.getFullYear();
      const currentYearCount = protocols.filter(p => p.startDate && p.startDate.startsWith(year.toString())).length;
      const seq = String(currentYearCount + 1).padStart(3, '0');

      return `${t}${st}${seq}-${month}/${year}`;
  };

  const getSubTypeOptions = () => {
     if (formData.type === 'Solicitação') return ['Solicitação de Afastamento','Solicitação de Desligamento', 'Solicitação Conclusiva'];
     if (formData.targetMemberType === 'Diretoria') return ['Advertência', 'Afastamento Disciplinar', 'Exclusão'];
     return ['Advertência','Suspensão de Nível 1','Suspensão de Nível 2','Exclusão'];
  };

  const executeSubmit = () => {
     let protocolNum = formData.protocolNumber;
     if (!protocolNum) protocolNum = generateProtocolNumber(formData.type, formData.subType, formData.startDate);

     const newP = { ...formData, endDate: autoCalculatedEndDate, protocolNumber: protocolNum };
     let updatedProtocols = [...protocols];

     if (isConclusiva) {
        const closureDate = formData.startDate || getTodayLocalStr();
        updatedProtocols = updatedProtocols.map(p => {
           if (p.id === formData.targetProtocolId) {
              return { ...p, endDate: closureDate, isConcludedForced: true, notes: `${p.notes ? p.notes + ' | ' : ''}Encerrado antecipadamente pela Solicitação Conclusiva Nº. ${protocolNum}.` };
           }
           return p;
        });
     }

     if (newP.id) {
        setProtocols(updatedProtocols.map(p => p.id === newP.id ? newP : p));
     } else {
        setProtocols([...updatedProtocols, { ...newP, id: generateId() }]);
     }
     setShowForm(false);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (isConclusiva && !formData.targetProtocolId) {
       return alertAction("Para uma Solicitação Conclusiva, é necessário selecionar o processo em andamento.");
    }
    setPinModal({ isOpen: true, action: executeSubmit });
  };

  const getMemberName = (id) => {
    const m = members.find(x => x.id === id);
    return m ? (m.usualName || m.name) : 'Desconhecido';
  }

  const gridClass = viewMode === 'grid' ? "grid grid-cols-1 lg:grid-cols-2 gap-6" : "flex flex-col gap-4";

  return (
    <div className="space-y-6">
      <div className="flex justify-between border-b pb-4">
        <h2 className="text-2xl font-bold">Protocolos</h2>
        <div className="flex gap-4">
          <div className="bg-slate-200 p-1 rounded-lg flex"><button onClick={()=>setActiveSubTab('andamento')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='andamento'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Em Andamento</button><button onClick={()=>setActiveSubTab('concluidos')} className={`px-4 py-1.5 rounded font-bold transition-colors ${activeSubTab==='concluidos'?'bg-white text-blue-600 shadow-sm':'text-slate-600 hover:text-slate-800'}`}>Concluídas</button></div>
          {!showForm && <button onClick={() => { setFormData({ id: '', memberId: '', targetMemberType: 'Efetivo', type: 'Disciplinar', subType: 'Advertência', infraction: infraList[0], startDate: getTodayLocalStr(), duration: '', notes: '', targetProtocolId: '' }); setShowForm(true); }} className="bg-blue-600 text-white px-4 py-1.5 rounded-lg font-bold flex items-center gap-1 shadow-md hover:bg-blue-700 transition-colors"><Plus size={16}/> Novo</button>}
        </div>
      </div>

      {activeSubTab === 'concluidos' && !showForm && (
         <div className="flex gap-2">
            <button onClick={()=>setConcluidosTab('disciplinares')} className={`px-4 py-1.5 text-xs font-bold rounded-full transition-colors border ${concluidosTab==='disciplinares'?'bg-amber-100 text-amber-800 border-amber-300':'bg-white text-slate-500 hover:bg-slate-50 border-slate-200'}`}>Disciplinares ({concluidosDisc.length})</button>
            <button onClick={()=>setConcluidosTab('solicitacoes')} className={`px-4 py-1.5 text-xs font-bold rounded-full transition-colors border ${concluidosTab==='solicitacoes'?'bg-blue-100 text-blue-800 border-blue-300':'bg-white text-slate-500 hover:bg-slate-50 border-slate-200'}`}>Solicitações ({concluidosSoli.length})</button>
         </div>
      )}

      <SelectionBar selectedIds={selectedIds} allIds={allVisibleIds} onSelectAll={()=>setSelectedIds(allVisibleIds)} onDeselectAll={()=>setSelectedIds([])} onBulkDelete={handleBulkDelete} />

      {showForm && (
        <form onSubmit={handleSubmit} className="bg-white p-6 rounded-xl border grid grid-cols-1 sm:grid-cols-2 gap-4 shadow-sm">
           <div className="col-span-1 sm:col-span-2"><label className="block text-sm font-bold mb-1">Membro Afetado</label>
              <div className="flex gap-2">
                 <select className="w-1/3 p-2 border rounded-l font-bold text-slate-700 bg-slate-50" value={formData.targetMemberType} onChange={e=>{setFormData({...formData, targetMemberType: e.target.value, memberId: '', subType: e.target.value==='Diretoria'?'Advertência':formData.type==='Solicitação'?'Solicitação de Afastamento':'Advertência' })}}>
                    <option value="Efetivo">Efetivo</option>
                    <option value="Diretoria">Diretoria</option>
                 </select>
                 <select required className="flex-1 p-2 border border-l-0 rounded-r font-bold text-slate-700 bg-slate-50" value={formData.memberId} onChange={e=>setFormData({...formData, memberId: e.target.value, targetProtocolId: ''})}>
                    <option value="">Selecione...</option>
                    {activeMembersFiltered.map(m=><option key={m.id} value={m.id}>{m.usualName || m.name}</option>)}
                 </select>
              </div>
           </div>
           <div><label className="block text-sm font-bold mb-1">Natureza</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.type} onChange={e=>setFormData({...formData, type: e.target.value, subType: e.target.value==='Disciplinar'?'Advertência':'Solicitação de Afastamento', infraction: e.target.value==='Disciplinar'?infraList[0]:'Moti. Pessoal'})}><option>Disciplinar</option><option>Solicitação</option></select></div>
           <div><label className="block text-sm font-bold mb-1">Aplicação</label><select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.subType} onChange={e=>setFormData({...formData, subType: e.target.value, targetProtocolId: ''})}>{getSubTypeOptions().map(o=><option key={o}>{o}</option>)}</select></div>

           {/* Renderização Condicional baseada na Solicitação Conclusiva */}
           {isConclusiva ? (
             <div className="col-span-1 sm:col-span-2 space-y-4 border p-4 rounded-lg bg-blue-50/50">
                <div>
                   <label className="block text-sm font-bold mb-1 text-blue-800">Selecione o Processo em Andamento para Forçar Conclusão</label>
                   <select required className="w-full p-2 border border-blue-200 rounded font-bold text-slate-700 bg-white" value={formData.targetProtocolId} onChange={e=>setFormData({...formData, targetProtocolId: e.target.value})}>
                      <option value="">{availableTargetProtocols.length > 0 ? 'Selecione um processo...' : 'Nenhum processo em andamento para este membro'}</option>
                      {availableTargetProtocols.map(p => <option key={p.id} value={p.id}>Nº. {p.protocolNumber} - {p.subType} (Fim Previsto: {formatDateBR(p.endDate)})</option>)}
                   </select>
                </div>
                {selectedTargetProtocol && (
                   <div className="bg-white p-4 rounded border border-blue-200 text-sm space-y-2 shadow-sm break-words">
                      <p><b className="text-slate-500 uppercase tracking-wider text-[10px] block mb-0.5">Processo Alvo:</b> <span className="font-bold text-blue-700">Nº. {selectedTargetProtocol.protocolNumber}</span></p>
                      <p><b className="text-slate-500 uppercase tracking-wider text-[10px] block mb-0.5">Natureza/Aplicação:</b> {selectedTargetProtocol.type} / {selectedTargetProtocol.subType}</p>
                      <p><b className="text-slate-500 uppercase tracking-wider text-[10px] block mb-0.5">Infração/Motivo:</b> {selectedTargetProtocol.infraction}</p>
                   </div>
                )}
             </div>
           ) : (
             <div className="col-span-1 sm:col-span-2"><label className="block text-sm font-bold mb-1">{formData.type==='Disciplinar'?'Infração':'Motivação'}</label>
                {formData.type==='Disciplinar' ? (<select className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.infraction} onChange={e=>setFormData({...formData, infraction: e.target.value})}>{infraList.map(i=><option key={i}>{i}</option>)}</select>) : (<input required className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.infraction} onChange={e=>setFormData({...formData, infraction: e.target.value})}/>)}
             </div>
           )}

           <div><label className="block text-sm font-bold mb-1">Data Deferimento</label><input required type="date" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.startDate} onChange={e=>setFormData({...formData, startDate: e.target.value})}/></div>
           {isDurational && <div><label className="block text-sm font-bold mb-1">Duração (Dias)</label><input required type="number" min="1" className="w-full p-2 border rounded font-bold text-slate-700 bg-slate-50" value={formData.duration} onChange={e=>setFormData({...formData, duration: e.target.value})}/></div>}
           {isDurational && <div className="col-span-1 sm:col-span-2"><label className="block text-sm font-bold mb-1">Data de Finalização Automática</label><input disabled className="w-full p-2 border rounded bg-slate-100 font-bold text-slate-500" value={autoCalculatedEndDate ? formatDateBR(autoCalculatedEndDate) : 'Calculado após informar a duração...'}/></div>}

           <div className="col-span-1 sm:col-span-2"><label className="block text-sm font-bold mb-1">Observações</label><textarea className="w-full p-2 border rounded" value={formData.notes} onChange={e=>setFormData({...formData, notes: e.target.value})}></textarea></div>
           <div className="col-span-1 sm:col-span-2 text-right text-xs text-amber-600 mb-2 font-bold">{['Exclusão', 'Solicitação de Desligamento'].includes(formData.subType) ? "Aviso: Este protocolo moverá o integrante para o Histórico de Ex-Membros imediatamente." : isConclusiva ? "Aviso: O processo alvo será encerrado imediatamente na data de deferimento." : ""}</div>
           <div className="col-span-1 sm:col-span-2 text-right flex justify-end gap-2 mt-4 border-t pt-4">
             <button type="button" onClick={() => setShowForm(false)} className="px-6 py-2 border rounded-lg text-slate-600 font-bold hover:bg-slate-50 transition-colors">Cancelar</button>
             <button type="submit" className="bg-slate-900 text-white px-6 py-2 rounded-lg font-bold shadow-md hover:bg-slate-800 transition-colors">Salvar Protocolo</button>
           </div>
        </form>
      )}

      <div className={gridClass
