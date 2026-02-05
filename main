import React, { useState, useCallback, useRef, useMemo, useEffect } from 'react';
import { LayoutGrid, Pencil, ArrowUpCircle, ArrowDownCircle, Menu, Eye, EyeOff, ChevronRight, ChevronLeft, Check, FileText, Download, HelpCircle, X, Image as ImageIcon, Type, Loader2, GripVertical, Lock, Trash2, Plus, Globe, Award, Star, BookOpen, Lightbulb, Zap, LayoutTemplate, Rocket, Cloud, FileCode } from 'lucide-react'; 

// --- 1. GLOBAL CONSTANTS ---

const FONT_CLASS = 'font-sans'; 
const STORAGE_KEY = 'resume-maker-local-draft-v1';

const UI = {
    BG_PRIMARY: 'bg-[#191919]', 
    BG_SECONDARY: 'bg-[#202020]',
    TEXT_PRIMARY: 'text-[#EDEDED]',
    TEXT_SECONDARY: 'text-[#B5B5B5]',
    BORDER: 'border-[#2A2A2A]',
    INPUT_BG: 'bg-[#191919]', 
    BUTTON_PRIMARY_BG: 'bg-[#FACC15]', 
    BUTTON_PRIMARY_HOVER: 'hover:bg-[#EAB308]',
    BUTTON_TEXT_CONTRAST: 'text-black font-bold', 
    BUTTON_TEXT_LIGHT: 'text-white font-bold',
    BUTTON_SUCCESS_BG: 'bg-[#2EA043]',
    BUTTON_WARNING_BG: 'bg-[#F7C948]',
    BUTTON_ERROR_BG: 'bg-[#E5533D]',
    HOVER_BG: 'hover:bg-[#2A2A2A]', 
};

const FORMATS = {
  CHRONOLOGICAL: 'Chronological',
  FUNCTIONAL: 'Functional',
  ENTRY_LEVEL: 'Entry-Level',
};

const FORMAT_SECTION_ORDERS = {
  [FORMATS.CHRONOLOGICAL]: ['personal', 'summary', 'skills', 'experience', 'education', 'certifications'],
  [FORMATS.FUNCTIONAL]: ['personal', 'summary', 'skills', 'education', 'experience', 'certifications'],
  [FORMATS.ENTRY_LEVEL]: ['personal', 'education', 'summary', 'skills', 'certifications', 'experience'],
};

const templates = {
  classic: { name: 'Classic', layout: 'single' },
  modern: { name: 'Modern', layout: 'two-column', sidebarBg: 'bg-white' },
  bold: { name: 'Bold', layout: 'single' },
  professional: { name: 'Professional', layout: 'two-column', sidebarBg: 'bg-gray-100' },
  executive: { name: 'Executive', layout: 'single' },
};

const sectionDefinitions = {
  personal: { title: 'Contact information', icon: <Pencil size={14} className="text-white" /> },
  summary: { title: 'Professional summary', icon: <FileText size={14} className="text-white" /> },
  skills: { title: 'Key skills', icon: <Check size={14} className="text-white" /> },
  experience: { title: 'Professional experience', icon: <LayoutGrid size={14} className="text-white" /> },
  education: { title: 'Education', icon: <FileText size={14} className="text-white" /> },
  certifications: { title: 'Projects & certifications', icon: <Check size={14} className="text-white" /> },
  languages: { title: 'Languages', icon: <Globe size={14} className="text-white" /> },
  awards: { title: 'Awards & honors', icon: <Award size={14} className="text-white" /> },
  custom: { title: 'Custom section', icon: <Star size={14} className="text-white" /> },
};

const initialResumeData = {
  format: FORMATS.CHRONOLOGICAL, 
  personal: {
    name: 'Binod', 
    phone: '(555) 123-4567',
    email: 'binod@email.com',
    linkedin: 'https://linkedin.com/in/binod',
    location: 'Mumbai, India'
  },
  summary: 'Highly analytical and results-driven Professional with 7+ years of experience in full-stack development. Proven ability to lead cross-functional teams, reduce system latency, and deliver mission-critical applications on schedule.',
  skills: [
    { name: 'Technical', list: 'Python, JavaScript, TypeScript, Go' },
    { name: 'Cloud & tools', list: 'AWS (EC2, Lambda, S3), Docker, Kubernetes' },
  ],
  experience: [
    { id: 1, title: 'Senior Software Engineer', company: 'TechCorp Innovations', duration: 'Jan 2021 – Present',
      bullets: [
        'Architected and implemented a microservices-based order processing system using Python and AWS Lambda.',
        'Led a team of 4 engineers in migrating legacy monolithic APIs to a modern GraphQL endpoint.'
      ]
    },
  ],
  education: [
    { id: 1, degree: 'B.Tech in Computer Science', institution: 'IIT Bombay', year: 'May 2018' },
  ],
  certifications: [
    { id: 1, name: 'AWS Certified Solutions Architect – Associate' }
  ],
  languages: [
      { id: 1, language: 'English', proficiency: 'Native' }
  ],
  awards: [
      { id: 1, title: 'Employee of the year' }
  ],
  customTitle: 'Volunteering',
  custom: [
      { id: 1, title: 'Community lead', description: 'Organized weekend food drives at the local shelter.'}
  ]
};

// --- 2. UI UTILITY COMPONENTS ---

const Logo = () => (
    <div className="flex items-center">
        <img 
            src="https://static.wixstatic.com/media/372fc4_a319bc427d6c46c9941698b861aa6711~mv2.png/v1/fill/w_288,h_120,al_c,q_85,usm_0.66_1.00_0.01,enc_avif,quality_auto/372fc4_a319bc427d6c46c9941698b861aa6711~mv2.png" 
            alt="Resume makero" 
            className="h-10 w-auto object-contain brightness-110"
        />
    </div>
);

const ProgressBar = ({ progress, lastSaved }) => (
    <div className="w-full">
        <div className="flex justify-between items-end mb-1 px-1">
            <div className="flex items-center gap-2">
                <span className="text-[10px] font-bold text-[#EDEDED]">Progress</span>
                {lastSaved && (
                    <div className="flex items-center gap-1 text-[9px] text-[#B5B5B5] animate-fadeIn">
                        <Cloud size={10} className="text-green-500" />
                        <span>Autosaved</span>
                    </div>
                )}
            </div>
            <span className="text-[10px] text-[#FACC15] font-bold">{Math.round(progress)}%</span>
        </div>
        <div className="w-full h-1.5 bg-[#2A2A2A] relative overflow-hidden rounded-full">
            <div 
                className="h-full bg-[#FACC15] transition-all duration-[800ms] ease-in-out" 
                style={{ width: progress + '%' }}
            />
        </div>
    </div>
);

const WebAppFooter = () => (
    <div className="text-center text-[#B5B5B5] text-[13px] py-4 mt-auto border-t border-[#2A2A2A] print:hidden">
        Made with love by YWS
    </div>
);

const InputField = ({ label, type = 'text', value, onChange, name, className = '', children }) => (
  <div className={className + " mb-3"}>
    <label className="block text-sm font-medium text-[#B5B5B5] mb-1 flex items-center">{label}{children}</label>
    {type === 'textarea' ? (
      <textarea name={name} value={value} onChange={onChange} rows="3" className="mt-1 block w-full rounded-md border border-[#2A2A2A] bg-[#191919] text-[#EDEDED] p-2 shadow-sm focus:border-[#FACC15] focus:ring-[#FACC15] text-sm outline-none transition-colors" />
    ) : (
      <input type={type} name={name} value={value} onChange={onChange} className="mt-1 block w-full rounded-md border border-[#2A2A2A] bg-[#191919] text-[#EDEDED] p-2 shadow-sm focus:border-[#FACC15] focus:ring-[#FACC15] text-sm outline-none transition-colors" />
    )}
  </div>
);

// --- 3. MODAL COMPONENTS ---

const ModalOverlay = ({ children, onClose }) => (
    <div className="fixed inset-0 bg-black/80 z-[100] flex items-center justify-center p-4 backdrop-blur-sm animate-fadeIn">
        <div className="absolute inset-0" onClick={onClose}></div>
        <div className="relative bg-[#202020] border border-[#2A2A2A] rounded-xl shadow-2xl w-full max-w-2xl max-h-[90vh] flex flex-col overflow-hidden">
            {children}
        </div>
    </div>
);

const TipsModal = ({ onClose }) => {
    const tips = [
        { title: "Quantify achievements", text: "Instead of 'Managed a team', use 'Managed a team of 10 and increased efficiency by 25%'." },
        { title: "Tailor to keywords", text: "Match skills and titles to the job description to pass ATS filters." },
        { title: "Keep it clean", text: "Use consistent formatting and avoid excessive graphics." }
    ];
    return (
        <ModalOverlay onClose={onClose}>
            <div className="p-4 border-b border-[#2A2A2A] flex justify-between items-center bg-[#191919]">
                <div className="flex items-center">
                    <Lightbulb size={20} className="text-[#FACC15] mr-2" />
                    <h3 className="text-lg font-bold text-[#EDEDED]">Professional advice</h3>
                </div>
                <button onClick={onClose} className="text-[#B5B5B5] hover:text-white"><X size={20} /></button>
            </div>
            <div className="p-6 overflow-y-auto max-h-[60vh]">
                <div className="grid gap-4">
                    {tips.map((tip, index) => (
                        <div key={index} className="p-4 rounded-lg border border-[#2A2A2A] bg-[#191919]">
                            <h4 className="text-md font-bold text-[#EDEDED] mb-1">{tip.title}</h4>
                            <p className="text-sm text-[#B5B5B5]">{tip.text}</p>
                        </div>
                    ))}
                </div>
            </div>
            <div className="p-4 border-t border-[#2A2A2A] bg-[#191919] flex justify-end">
                <button onClick={onClose} className="px-4 py-2 rounded-md bg-[#FACC15] text-black font-bold">Got it</button>
            </div>
        </ModalOverlay>
    );
};

const ExamplesModal = ({ category, onClose, onUse }) => {
    const examples = [
        "Led high-impact engineering projects resulting in 30% faster deployment cycles.",
        "Managed cross-functional teams of 12 people to deliver innovative product features.",
        "Streamlined operational workflows reducing annual costs by approximately $50,000."
    ];
    return (
        <ModalOverlay onClose={onClose}>
            <div className="p-4 border-b border-[#2A2A2A] flex justify-between items-center bg-[#191919]">
                <div className="flex items-center">
                    <Zap size={20} className="text-[#FACC15] mr-2" />
                    <h3 className="text-lg font-bold text-[#EDEDED]">Inspiration library</h3>
                </div>
                <button onClick={onClose} className="text-[#B5B5B5] hover:text-white"><X size={20} /></button>
            </div>
            <div className="p-6 overflow-y-auto max-h-[60vh]">
                <div className="space-y-3">
                    {examples.map((ex, index) => (
                        <div key={index} className="p-4 rounded-lg border border-[#2A2A2A] bg-[#191919] hover:border-[#FACC15] transition group">
                            <p className="text-sm text-[#EDEDED] mb-3">{ex}</p>
                            <button onClick={() => onUse(ex)} className="text-xs flex items-center text-[#B5B5B5] group-hover:text-[#FACC15] font-bold">
                                <Plus size={14} className="mr-1"/> Use this example
                            </button>
                        </div>
                    ))}
                </div>
            </div>
        </ModalOverlay>
    );
};

const DownloadAdModal = ({ format, onClose }) => {
    const [isLoading, setIsLoading] = useState(true);
    useEffect(() => { const timer = setTimeout(() => setIsLoading(false), 2500); return () => clearTimeout(timer); }, []);
    
    const handleDownload = () => { 
        if (format === 'pdf') {
            onClose();
            setTimeout(() => window.print(), 100);
        } else if (format === 'docx') {
            const header = "<html xmlns:o='urn:schemas-microsoft-com:office:office' xmlns:w='urn:schemas-microsoft-com:office:word' xmlns='http://www.w3.org/TR/REC-html40'><head><meta charset='utf-8'><title>Resume</title><style>body { font-family: Arial, sans-serif; }</style></head><body>";
            const footer = "</body></html>";
            const element = document.getElementById("resume-preview-container");
            if (!element) return;
            const sourceHTML = header + element.innerHTML + footer;
            const blob = new Blob(['\ufeff', sourceHTML], { type: 'application/msword' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            link.href = url;
            link.download = 'resume.doc';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            onClose();
        }
    };

    return (
        <ModalOverlay onClose={onClose}>
            <div className="p-8 flex flex-col items-center justify-center text-center min-h-[400px] w-full">
                {!isLoading && <div className="w-full flex justify-end mb-2 absolute top-4 right-4"><button onClick={onClose} className="text-[#B5B5B5] hover:text-white"><X size={24} /></button></div>}
                {isLoading ? (
                    <div className="flex flex-col items-center">
                        <Loader2 size={48} className="text-[#FACC15] animate-spin mb-4" />
                        <h2 className="text-2xl font-bold text-[#EDEDED] mb-3">Finalizing {format.toUpperCase()}...</h2>
                    </div>
                ) : (
                    <div className="w-full flex flex-col items-center">
                        <Check size={48} className="text-green-500 mb-4" />
                        <h2 className="text-2xl font-bold text-[#EDEDED] mb-2">Download ready!</h2>
                        <button onClick={handleDownload} className="mt-6 w-full max-w-sm py-3 rounded-lg bg-[#FACC15] text-black font-bold text-lg shadow-xl hover:scale-105 transition-transform">Download now</button>
                    </div>
                )}
            </div>
        </ModalOverlay>
    );
};

// --- 4. DATA MAPS (EDITORS & VIEWERS) ---

const EditorsMap = {
    personal: ({ data, setData }) => {
        const handleChange = (e) => setData(p => ({ ...p, personal: { ...p.personal, [e.target.name]: e.target.value } }));
        return (
            <div className="grid grid-cols-2 gap-4">
                <InputField label="Full name" name="name" value={data.personal.name} onChange={handleChange} className="col-span-2" />
                <InputField label="Phone" name="phone" value={data.personal.phone} onChange={handleChange} />
                <InputField label="Email" name="email" value={data.personal.email} onChange={handleChange} />
                <InputField label="LinkedIn" name="linkedin" value={data.personal.linkedin} onChange={handleChange} />
                <InputField label="City, state" name="location" value={data.personal.location} onChange={handleChange} />
            </div>
        );
    },
    summary: ({ data, setData, openExamples }) => (
        <div className="space-y-2">
            <div className="flex justify-between items-center"><label className="text-sm font-medium text-gray-300">Summary</label><button onClick={() => openExamples('summary')} className="text-xs text-yellow-500 font-bold border border-yellow-500/20 px-2 py-1 rounded hover:bg-yellow-500/10 transition-colors">Inspiration</button></div>
            <textarea value={data.summary} onChange={e => setData(p => ({...p, summary: e.target.value}))} rows="4" className="w-full rounded border border-[#2A2A2A] bg-[#191919] text-[#EDEDED] p-2 text-sm focus:border-[#FACC15] outline-none transition-colors" />
        </div>
    ),
    skills: ({ data, setData }) => (
        <div className="space-y-4">
            {data.skills.map((s, i) => (
                <div key={i} className="p-3 rounded border border-[#2A2A2A] bg-[#191919]">
                    <div className="flex justify-between items-center mb-2"><span className="text-xs font-bold text-gray-500 uppercase">Group {i+1}</span><button onClick={() => setData(p => ({...p, skills: p.skills.filter((_, idx) => idx !== i)}))} className="text-red-500 hover:text-red-400 transition-colors"><Trash2 size={12}/></button></div>
                    <InputField label="Label" value={s.name} onChange={e => {const n=[...data.skills]; n[i]={...n[i], name: e.target.value}; setData(p=>({...p, skills:n}))}} />
                    <InputField label="Items" type="textarea" value={s.list} onChange={e => {const n=[...data.skills]; n[i]={...n[i], list: e.target.value}; setData(p=>({...p, skills:n}))}} />
                </div>
            ))}
            <button onClick={() => setData(p => ({...p, skills: [...p.skills, {name: '', list: ''}]}))} className="w-full py-2 border border-dashed border-[#444] rounded text-gray-400 text-sm hover:text-white transition">+ Add category</button>
        </div>
    ),
    experience: ({ data, setData, openExamples }) => (
        <div className="space-y-4">
            <div className="flex justify-end"><button onClick={() => openExamples('experience')} className="text-xs text-yellow-500 font-bold border border-yellow-500/20 px-2 py-1 rounded hover:bg-yellow-500/10 transition-colors">View examples</button></div>
            {data.experience.map((exp, i) => <div key={exp.id} className="p-3 border border-[#333] rounded"><InputField label="Title" value={exp.title} onChange={e => {const n=[...data.experience]; n[i]={...n[i], title: e.target.value}; setData(p=>({...p, experience:n}))}} /><InputField label="Company" value={exp.company} onChange={e => {const n=[...data.experience]; n[i]={...n[i], company: e.target.value}; setData(p=>({...p, experience:n}))}} /><InputField label="Bullets" type="textarea" value={exp.bullets.join('\n')} onChange={e => {const n=[...data.experience]; n[i]={...n[i], bullets: e.target.value.split('\n')}; setData(p=>({...p, experience:n}))}} /><button onClick={() => setData(p => ({...p, experience: p.experience.filter((_, idx) => idx !== i)}))} className="text-red-500 text-xs mt-2 hover:underline">Remove position</button></div>)}
            <button onClick={() => setData(p => ({...p, experience: [...p.experience, {id: Date.now(), title: '', company: '', bullets: ['']}]}))} className="w-full py-2 border border-dashed border-[#444] rounded text-gray-400 text-sm hover:text-white transition">+ Add position</button>
        </div>
    ),
    education: ({ data, setData }) => (
        <div className="space-y-4">
            {data.education.map((ed, i) => <div key={ed.id} className="p-3 border border-[#333] rounded"><InputField label="Degree" value={ed.degree} onChange={e => {const n=[...data.education]; n[i]={...n[i], degree: e.target.value}; setData(p=>({...p, education:n}))}} /><InputField label="School" value={ed.institution} onChange={e => {const n=[...data.education]; n[i]={...n[i], institution: e.target.value}; setData(p=>({...p, education:n}))}} /><button onClick={() => setData(p => ({...p, education: p.education.filter((_, idx) => idx !== i)}))} className="text-red-500 text-xs mt-2 hover:underline">Remove education</button></div>)}
            <button onClick={() => setData(p => ({...p, education: [...p.education, {id: Date.now(), degree: '', institution: ''}]}))} className="w-full py-2 border border-dashed border-[#444] rounded text-gray-400 text-sm hover:text-white transition">+ Add education</button>
        </div>
    ),
    certifications: ({ data, setData }) => (
        <div className="space-y-3">{data.certifications.map((c, i) => <div key={c.id} className="p-2 border border-[#333] rounded"><InputField label="Name" value={c.name} onChange={e => {const n=[...data.certifications]; n[i]={...n[i], name: e.target.value}; setData(p=>({...p, certifications:n}))}} /><button onClick={() => setData(p => ({...p, certifications: p.certifications.filter((_, idx) => idx !== i)}))} className="text-red-500 text-xs hover:underline">Remove</button></div>)}<button onClick={() => setData(p => ({...p, certifications: [...p.certifications, {id: Date.now(), name: ''}]}))} className="w-full py-2 border border-dashed border-[#444] rounded text-gray-400 text-sm hover:text-white transition">+ Add certification</button></div>
    ),
    languages: ({ data, setData }) => (
        <div className="space-y-3">{data.languages.map((l, i) => <div key={l.id} className="grid grid-cols-2 gap-2"><InputField label="Language" value={l.language} onChange={e => {const n=[...data.languages]; n[i]={...n[i], language: e.target.value}; setData(p=>({...p, languages:n}))}} /><InputField label="Level" value={l.proficiency} onChange={e => {const n=[...data.languages]; n[i]={...n[i], proficiency: e.target.value}; setData(p=>({...p, languages:n}))}} /></div>)}<button onClick={() => setData(p => ({...p, languages: [...p.languages, {id: Date.now(), language: '', proficiency: ''}]}))} className="w-full py-2 border border-dashed border-[#444] rounded text-gray-400 text-sm hover:text-white transition">+ Add language</button></div>
    ),
    awards: ({ data, setData }) => (
        <div className="space-y-3">{data.awards.map((a, i) => <div key={a.id} className="p-2 border border-[#333] rounded"><InputField label="Award" value={a.title} onChange={e => {const n=[...data.awards]; n[i]={...n[i], title: e.target.value}; setData(p=>({...p, awards:n}))}} /><button onClick={() => setData(p => ({...p, awards: p.awards.filter((_, idx) => idx !== i)}))} className="text-red-500 text-xs hover:underline">Remove</button></div>)}<button onClick={() => setData(p => ({...p, awards: [...p.awards, {id: Date.now(), title: ''}]}))} className="w-full py-2 border border-dashed border-[#444] rounded text-gray-400 text-sm hover:text-white transition">+ Add award</button></div>
    ),
    custom: ({ data, setData }) => (
        <div className="space-y-4">
            <InputField label="Section title" value={data.customTitle} onChange={e => setData(p => ({...p, customTitle: e.target.value}))} />
            {data.custom.map((c, i) => (
                <div key={c.id} className="p-3 border border-[#333] rounded"><InputField label="Item title" value={c.title} onChange={e => {const n=[...data.custom]; n[i]={...n[i], title: e.target.value}; setData(p=>({...p, custom:n}))}} /><InputField label="Desc" type="textarea" value={c.description} onChange={e => {const n=[...data.custom]; n[i]={...n[i], description: e.target.value}; setData(p=>({...p, custom:n}))}} /><button onClick={() => setData(p => ({...p, custom: p.custom.filter((_, idx) => idx !== i)}))} className="text-red-500 text-xs hover:underline">Remove item</button></div>
            ))}
            <button onClick={() => setData(p => ({...p, custom: [...p.custom, {id: Date.now(), title: '', description: ''}]}))} className="w-full py-2 border border-dashed border-[#444] rounded text-gray-400 text-sm hover:text-white transition">+ Add item</button>
        </div>
    )
};

const SectionViewerMap = {
    summary: ({ data }) => <div className="mb-4 text-black text-[12pt] leading-relaxed text-justify">{data}</div>,
    skills: ({ data }) => <div className="mb-4 text-[12pt]">{data.map((s,i)=><div key={i} className="mb-2"><span className="font-bold block text-gray-800">{s.name}</span><span className="text-black">{s.list}</span></div>)}</div>,
    experience: ({ data }) => <div className="mb-4">{data.map((j,i)=><div key={j.id} className="mb-4"><div className="flex justify-between items-baseline font-bold text-[13pt]"><span>{j.title}</span><span className="text-[11pt] font-normal">{j.duration}</span></div><div className="text-[12pt] font-semibold italic mb-1 text-gray-800">{j.company}</div><ul className="list-disc ml-5 space-y-1">{j.bullets.map((b,k)=><li key={k} className="text-[12pt]" dangerouslySetInnerHTML={{__html:b.replace(/\*\*(.*?)\*\*/g,'<b>$1</b>')}}/>)}</ul></div>)}</div>,
    education: ({ data }) => <div className="mb-4">{data.map((e,i)=><div key={e.id} className="mb-3"><div className="flex justify-between font-bold text-[12pt]"><span>{e.degree}</span></div><div className="italic text-[12pt]">{e.institution}</div></div>)}</div>,
    certifications: ({ data }) => <div className="mb-4">{data.map((c,i)=><div key={c.id} className="mb-2 text-[12pt] font-bold">{c.name}</div>)}</div>,
    languages: ({ data }) => <div className="mb-4">{data.map((l,i)=><div key={l.id} className="flex justify-between text-[12pt] mb-1"><span className="font-bold">{l.language}</span><span>{l.proficiency}</span></div>)}</div>,
    awards: ({ data }) => <div className="mb-4">{data.map((a,i)=><div key={a.id} className="mb-1 font-bold text-[12pt]">{a.title}</div>)}</div>,
    custom: ({ data }) => <div className="mb-4">{data.map((c,i)=><div key={c.id} className="mb-3"><div className="flex justify-between font-bold"><span>{c.title}</span></div><div className="text-[12pt] text-gray-700">{c.description}</div></div>)}</div>,
};

// --- 5. VIEWER HELPERS ---

const SectionHeader = ({ title, templateKey }) => {
    let classes = "font-bold text-[16pt] mt-4 mb-2 uppercase text-[#111111] ";
    let borderStyle = "border-b border-gray-300 pb-1";
    if (templateKey === 'bold') borderStyle = "border-b-4 border-black pb-1";
    if (templateKey === 'executive') borderStyle = "border-b border-gray-400 pb-1 text-center tracking-widest";
    if (templateKey === 'modern') borderStyle = "text-[#2383E2] border-b border-gray-200 pb-1";
    if (templateKey === 'professional') borderStyle = "text-gray-800 border-b border-gray-300 pb-1";
    return <h2 className={classes + borderStyle}>{title}</h2>;
};

const PersonalSectionViewer = ({ data, templateKey }) => {
    const isCentered = templateKey === 'classic' || templateKey === 'executive';
    const alignClass = isCentered ? 'text-center' : 'text-left';
    const justifyClass = isCentered ? 'justify-center' : 'justify-start';
    const borderClass = (templateKey === 'modern' || templateKey === 'professional') ? '' : 'border-b border-[#CCCCCC]';
    return (
      <div className={alignClass + " mb-4 pb-4 " + borderClass}>
        <h1 className="text-5xl font-extrabold mb-2 text-black">{data.name}</h1>
        <div className={"text-[12pt] flex flex-wrap gap-x-3 gap-y-1 text-black " + justifyClass}>
          <span>{data.phone}</span><span>•</span><span>{data.email}</span><span>•</span><span>{data.location}</span>
        </div>
      </div>
    );
};

const ResumeViewer = ({ data, templateKey, font, previewMode = false }) => {
  const currentOrder = data.format === 'Custom' && data.customOrder ? data.customOrder : (FORMAT_SECTION_ORDERS[data.format] || FORMAT_SECTION_ORDERS[FORMATS.CHRONOLOGICAL]);
  const template = templates[templateKey];
  const isTwoColumn = template.layout === 'two-column';
  const containerClass = previewMode ? "bg-white text-black h-full w-full overflow-hidden relative " + font : font + " bg-white text-black print:p-0 min-h-[1100px] shadow-xl mx-auto";
  
  const sidebarSections = ['skills', 'languages', 'certifications', 'awards'];
  const mainSections = ['summary', 'experience', 'education', 'custom']; 

  const renderSection = (key) => {
      const SectionComponent = SectionViewerMap[key];
      const sectionData = data[key];
      if (!sectionData || (Array.isArray(sectionData) && sectionData.length === 0)) return null;
      const title = key === 'custom' ? (data.customTitle || 'Custom section') : sectionDefinitions[key].title;
      return (
        <div key={key}>
            <SectionHeader title={title} templateKey={templateKey} />
            <SectionComponent data={sectionData} />
        </div>
      );
  };

  return (
    <div id="resume-preview-container" className={containerClass + (isTwoColumn ? " flex flex-row h-full" : " p-10")}>
        {isTwoColumn ? (
            <>
                <div className={(template.sidebarBg || 'bg-gray-100') + " w-[32%] p-6 border-r border-gray-200 flex flex-col h-full"}>
                    <PersonalSectionViewer data={data.personal} templateKey={templateKey} />
                    <div className="space-y-4 mt-4">{currentOrder.filter(key => sidebarSections.includes(key)).map(renderSection)}</div>
                </div>
                <div className="w-[68%] p-8 pt-10"><div className="space-y-2">{currentOrder.filter(key => mainSections.includes(key)).map(renderSection)}</div></div>
            </>
        ) : (
            <>
                <PersonalSectionViewer data={data.personal} templateKey={templateKey} />
                {currentOrder.filter(k => k !== 'personal').map(renderSection)}
            </>
        )}
    </div>
  );
};

// --- 6. ONBOARDING STEP COMPONENTS ---

const Step0_DocType = ({ setState, nextStep }) => (
    <div className="space-y-6 animate-fadeIn">
        <h3 className="text-xl font-bold text-[#EDEDED]">Choose your path</h3>
        <div className="grid grid-cols-1 gap-4">
            <button onClick={() => { setState(p => ({...p, docType: 'Resume'})); nextStep(); }} className="p-5 rounded-lg border border-[#2A2A2A] bg-[#191919] hover:border-[#FACC15] transition text-left group">
                <div className="flex items-center justify-between mb-2"><span className="font-bold text-lg text-white group-hover:text-[#FACC15]">Resume</span><FileText size={24} className="text-gray-400 group-hover:text-[#FACC15]" /></div>
                <p className="text-sm text-gray-400">Concise for job applications.</p>
            </button>
            <button onClick={() => { setState(p => ({...p, docType: 'CV'})); nextStep(); }} className="p-5 rounded-lg border border-[#2A2A2A] bg-[#191919] hover:border-[#FACC15] transition text-left group">
                <div className="flex items-center justify-between mb-2"><span className="font-bold text-lg text-white group-hover:text-[#FACC15]">Curriculum vitae (CV)</span><BookOpen size={24} className="text-gray-400 group-hover:text-[#FACC15]" /></div>
                <p className="text-sm text-gray-400">Comprehensive for specialized roles.</p>
            </button>
        </div>
    </div>
);

const Step1_CareerInfo = ({ state, setState, nextStep, onSkip, onBack }) => {
    const handleChange = (e) => setState(prev => ({ ...prev, career: { ...prev.career, [e.target.name]: e.target.value } }));
    return (
        <div className="space-y-4">
            <div className="flex justify-between items-start">
                <h3 className="text-xl font-bold text-white">Career profile</h3>
                <button onClick={onSkip} className="text-xs font-medium text-gray-400 hover:text-white transition-colors underline pt-1">Skip</button>
            </div>
            <InputField label="Target job title" name="jobTitle" value={state.career.jobTitle} onChange={handleChange} />
            <InputField label="Industry" name="industry" value={state.career.industry} onChange={handleChange} />
            <div className="flex gap-3 mt-6">
                <button onClick={onBack} className="px-4 py-2 text-gray-400 hover:text-white transition-colors">Back</button>
                <button onClick={nextStep} className={`flex-1 bg-[#FACC15] text-black font-bold py-2 rounded transition-transform active:scale-95`}>Next step</button>
            </div>
        </div>
    );
};

const Step2_SmartQuestions = ({ nextStep, onBack }) => (
    <div className="space-y-4 text-center">
        <h3 className="text-xl font-bold text-white">Personalization</h3>
        <p className="text-sm text-gray-400">Answer a few more questions to optimize your layout.</p>
        <div className="flex gap-3 mt-6">
            <button onClick={onBack} className="px-4 py-2 text-gray-400 hover:text-white transition-colors">Back</button>
            <button onClick={nextStep} className="flex-1 bg-[#FACC15] text-black font-bold py-2 rounded transition-transform active:scale-95">Continue</button>
        </div>
    </div>
);

const Step3_FormatRecommendation = ({ setResumeData, setStep, onBack }) => {
    const handleStart = () => { setResumeData(prev => ({...prev, format: FORMATS.CHRONOLOGICAL})); setStep(4); };
    return (
        <div className="space-y-4 text-center">
            <h3 className="text-xl font-bold text-white">Setup complete</h3>
            <p className="text-sm text-gray-400 mb-6">Turbo-engine suggests a Chronological layout for you.</p>
            <div className="flex gap-3">
                <button onClick={onBack} className="px-4 py-2 text-gray-400 hover:text-white transition-colors">Back</button>
                <button onClick={handleStart} className="flex-1 bg-[#FACC15] text-black font-bold py-2 rounded transition-transform active:scale-95">Start building</button>
            </div>
        </div>
    );
};

// --- 7. SETTINGS PANELS ---

const SectionReorderPanel = ({ resumeData, setResumeData }) => {
    const currentOrder = resumeData.format === 'Custom' && resumeData.customOrder ? resumeData.customOrder : (FORMAT_SECTION_ORDERS[resumeData.format] || FORMAT_SECTION_ORDERS[FORMATS.CHRONOLOGICAL]);
    const availableSections = Object.keys(sectionDefinitions).filter(key => !currentOrder.includes(key));
    const [draggingIndex, setDraggingIndex] = useState(null);
    const [dragOverIndex, setDragOverIndex] = useState(null);

    const handleDragStart = (e, index) => {
        setDraggingIndex(index);
        e.dataTransfer.setData('text/plain', index);
        e.dataTransfer.effectAllowed = 'move';
    };

    const handleDragOver = (e, index) => {
        e.preventDefault();
        setDragOverIndex(index);
    };

    const handleDrop = (e, targetIndex) => {
        e.preventDefault();
        const sourceIndex = parseInt(e.dataTransfer.getData('text/plain'), 10);
        
        if (sourceIndex === targetIndex || targetIndex === 0 || sourceIndex === 0) {
            setDraggingIndex(null);
            setDragOverIndex(null);
            return;
        }

        const newOrder = [...currentOrder];
        const [movedItem] = newOrder.splice(sourceIndex, 1);
        newOrder.splice(targetIndex, 0, movedItem);

        setResumeData(prev => ({
            ...prev,
            format: 'Custom',
            customOrder: newOrder
        }));

        setDraggingIndex(null);
        setDragOverIndex(null);
    };

    return (
        <div className="p-4 bg-[#202020] border-b border-[#2A2A2A]">
            <h3 className="font-bold text-lg mb-4 text-[#EDEDED] border-b border-[#2A2A2A] pb-2">Section layout</h3>
            <div className="space-y-2 mb-6">
                {currentOrder.map((key, index) => (
                    <div 
                        key={key} 
                        draggable={index > 0}
                        onDragStart={(e) => handleDragStart(e, index)}
                        onDragOver={(e) => handleDragOver(e, index)}
                        onDrop={(e) => handleDrop(e, index)}
                        onDragEnd={() => { setDraggingIndex(null); setDragOverIndex(null); }}
                        className={`flex items-center justify-between p-2 rounded-md transition-all ${
                            index === 0 ? 'bg-gray-800 border-transparent opacity-60' : 'bg-[#191919] border cursor-grab active:cursor-grabbing'
                        } ${
                            dragOverIndex === index && index !== 0 ? 'border-[#FACC15] ring-2 ring-[#FACC15]/20' : 'border-[#2A2A2A]'
                        } ${
                            draggingIndex === index ? 'opacity-40 scale-95' : ''
                        }`}
                    >
                        <div className="flex items-center text-white">
                            {index > 0 ? <GripVertical size={16} className="mr-2" /> : <Lock size={14} className="mr-2" />}
                            <span className="text-sm font-medium text-white">{sectionDefinitions[key].title}</span>
                        </div>
                        {index > 0 && (
                            <div className="flex items-center space-x-1">
                                <button onClick={() => {
                                    const newOrder = [...currentOrder];
                                    const [moved] = newOrder.splice(index, 1);
                                    newOrder.splice(index - 1 > 0 ? index - 1 : 1, 0, moved);
                                    setResumeData(p => ({...p, format: 'Custom', customOrder: newOrder}));
                                }} disabled={index <= 1} className="p-1 text-white hover:text-[#FACC15] transition-colors"><ArrowUpCircle size={14}/></button>
                                <button onClick={() => {
                                    const newOrder = [...currentOrder];
                                    const [moved] = newOrder.splice(index, 1);
                                    newOrder.splice(index + 1 < newOrder.length ? index + 1 : newOrder.length, 0, moved);
                                    setResumeData(p => ({...p, format: 'Custom', customOrder: newOrder}));
                                }} disabled={index === currentOrder.length - 1} className="p-1 text-white hover:text-[#FACC15] transition-colors"><ArrowDownCircle size={14}/></button>
                                <button onClick={() => setResumeData(p => ({...p, format: 'Custom', customOrder: CurrentOrder.filter(k => k !== key)}))} className="text-red-500 ml-1 hover:text-red-400 transition-colors"><Trash2 size={14}/></button>
                            </div>
                        )}
                    </div>
                ))}
            </div>
            <div className="space-y-2">
                {availableSections.map(key => (
                    <div key={key} className={`flex items-center justify-between p-2 rounded border border-dashed border-[#444] bg-[#1a1a1a] hover:border-[#FACC15] transition-colors`}>
                        <div className="flex items-center text-white">
                            {sectionDefinitions[key].icon}
                            <span className="ml-2 text-sm">{sectionDefinitions[key].title}</span>
                        </div>
                        <button onClick={() => setResumeData(p => ({...p, format: 'Custom', customOrder: [...currentOrder, key]}))} className="text-[#FACC15] font-bold text-xs uppercase hover:underline">+ Add</button>
                    </div>
                ))}
            </div>
        </div>
    );
};

const TemplateThumbnail = ({ tKey, selected, onClick }) => {
    const isSelected = selected === tKey;
    const template = templates[tKey];
    const isTwoColumn = template.layout === 'two-column';
    const border = isSelected ? "border-[#FACC15] ring-2 ring-[#FACC15]/30" : "border-[#444] group-hover:border-[#666]";
    return (
        <div onClick={() => onClick(tKey)} className="cursor-pointer group flex flex-col space-y-2 transition-transform active:scale-95">
            <div className={"relative h-32 w-full bg-white rounded-md overflow-hidden transition-all duration-200 border-2 " + border}>
                <div className={"w-full h-full p-2 flex " + (isTwoColumn ? 'flex-row' : 'flex-col')}>
                    {isTwoColumn ? (
                        <>
                            <div className={(template.sidebarBg === 'bg-gray-100' ? 'bg-gray-200' : 'border-r border-gray-100') + " w-1/3 h-full pr-1 pt-1"}>
                                <div className="h-1.5 w-6 bg-gray-800 mb-1"></div>
                                <div className="h-1 w-full bg-gray-300"></div>
                            </div>
                            <div className="w-2/3 h-full pl-2 pt-1">
                                <div className="h-2 w-12 bg-gray-900 mb-1"></div>
                                <div className="h-1 w-full bg-gray-300"></div>
                            </div>
                        </>
                    ) : (
                        <div className={"w-full h-full flex flex-col " + (tKey === 'classic' || tKey === 'executive' ? 'items-center' : 'items-start')}>
                            <div className="h-2 w-1/2 bg-gray-900 mb-1"></div>
                            <div className="h-1 w-full bg-gray-300"></div>
                        </div>
                    )}
                </div>
            </div>
            <div className="text-center"><p className={"text-[10px] font-bold uppercase " + (isSelected ? 'text-[#FACC15]' : 'text-gray-400')}>{template.name}</p></div>
        </div>
    );
};

// --- 8. MAIN APP COMPONENT ---

const App = () => {
  const [currentStep, setCurrentStep] = useState(0); 
  const [resumeData, setResumeData] = useState(initialResumeData);
  const [templateKey, setTemplateKey] = useState('classic');
  const [font, setFont] = useState('font-sans'); 
  const [editingSection, setEditingSection] = useState('personal');
  const [showDownload, setShowDownload] = useState(false);
  const [previewPanelOpen, setPreviewPanelOpen] = useState(true);
  const [tipsOpen, setTipsOpen] = useState(false);
  const [examplesOpen, setExamplesOpen] = useState(false);
  const [exampleCategory, setExampleCategory] = useState('summary');
  const [adModalOpen, setAdModalOpen] = useState(false);
  const [downloadFormat, setDownloadFormat] = useState('pdf');
  const [flowState, setFlowState] = useState({ docType: 'Resume', career: { jobTitle: '', industry: '', level: 'entry', targetCompany: '' } });
  
  // Local Storage State
  const [isInitialLoad, setIsInitialLoad] = useState(true);
  const [lastSaved, setLastSaved] = useState(null);

  // Load from LocalStorage
  useEffect(() => {
    const saved = localStorage.getItem(STORAGE_KEY);
    if (saved) {
        try {
            const data = JSON.parse(saved);
            if (data.resumeData) setResumeData(data.resumeData);
            if (data.currentStep !== undefined) setCurrentStep(data.currentStep);
            if (data.templateKey) setTemplateKey(data.templateKey);
            if (data.font) setFont(data.font);
            if (data.flowState) setFlowState(data.flowState);
        } catch (e) {
            console.error("Failed to load saved draft:", e);
        }
    }
    setIsInitialLoad(false);
  }, []);

  // Autosave to LocalStorage
  useEffect(() => {
    if (isInitialLoad) return;

    const saveTimeout = setTimeout(() => {
        const payload = { resumeData, currentStep, templateKey, font, flowState };
        localStorage.setItem(STORAGE_KEY, JSON.stringify(payload));
        setLastSaved(new Date().toLocaleTimeString());
    }, 1000);

    return () => clearTimeout(saveTimeout);
  }, [resumeData, currentStep, templateKey, font, flowState, isInitialLoad]);

  const CurrentOrder = resumeData.format === 'Custom' && resumeData.customOrder ? resumeData.customOrder : (FORMAT_SECTION_ORDERS[resumeData.format] || FORMAT_SECTION_ORDERS[FORMATS.CHRONOLOGICAL]);
  const currentSectionIndex = CurrentOrder.indexOf(editingSection);
  const hasPreviousSection = currentSectionIndex > 0;
  const hasNextSection = currentSectionIndex < CurrentOrder.length - 1;

  const goToPreviousSection = () => {
      if (hasPreviousSection) {
          setEditingSection(CurrentOrder[currentSectionIndex - 1]);
      } else {
          setCurrentStep(3);
      }
  };
  
  const goToNextSection = () => hasNextSection ? setEditingSection(CurrentOrder[currentSectionIndex + 1]) : setShowDownload(true);
  const openExamples = (cat) => { setExampleCategory(cat); setExamplesOpen(true); };

  const progress = useMemo(() => { 
      if (showDownload) return 100; 
      if (currentStep < 4) return Math.min(20, 5 + (currentStep * 5)); 
      let p = 20; 
      const isModified = (val, initVal) => (val && val !== initVal);
      const init = initialResumeData;
      if (isModified(resumeData.personal.name, init.personal.name)) p += 10; 
      if (resumeData.experience.length > 0 && isModified(resumeData.experience[0].title, init.experience[0].title)) p += 15; 
      if (resumeData.skills.length > 0) p += 10; 
      if (isModified(resumeData.summary, init.summary)) p += 10; 
      if (resumeData.education.length > 0) p += 10; 
      if (resumeData.certifications.length > 0) p += 5; 
      return Math.min(p, 80); 
  }, [currentStep, showDownload, resumeData]);

  const CurrentEditor = EditorsMap[editingSection];

  return (
    <div className={"min-h-screen " + UI.BG_PRIMARY + " flex flex-col md:flex-row " + FONT_CLASS + " print:block"}>
      <style>{`@import url('https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,100..1000;1,9..40,100..1000&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Merriweather:ital,wght@0,300;0,400;0,700;1,300;1,400;1,700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:ital,wght@0,100..700;1,100..700&display=swap');
.font-sans { font-family: 'DM Sans', sans-serif !important; } .font-serif { font-family: 'Merriweather', serif !important; } .font-mono { font-family: 'Roboto Mono', monospace !important; }`}</style>
      
      {tipsOpen && <TipsModal onClose={() => setTipsOpen(false)} />}
      {examplesOpen && <ExamplesModal category={exampleCategory} onClose={() => setExamplesOpen(false)} onUse={(text) => { if (exampleCategory === 'summary') setResumeData(p => ({...p, summary: text})); if (exampleCategory === 'experience') setResumeData(p => ({...p, experience: [{...p.experience[0], bullets: [text]}, ...p.experience.slice(1)]})); setExamplesOpen(false); }} />}
      {adModalOpen && <DownloadAdModal format={downloadFormat} onClose={() => setAdModalOpen(false)} />}

      <div className={"w-full " + (previewPanelOpen ? 'md:w-96 xl:w-1/3 border-r' : 'md:max-w-2xl md:mx-auto') + " " + UI.BG_SECONDARY + " " + UI.BORDER + " shadow-lg overflow-y-auto flex flex-col print:hidden"}>
        <div className={"p-4 flex justify-between items-center border-b " + UI.BORDER + " " + UI.BG_PRIMARY}>
            <Logo />
            <div className="flex items-center space-x-2">
                <button onClick={() => setTipsOpen(true)} className="p-2 text-gray-400 hover:text-[#FACC15] transition-colors" title="Advice"><Lightbulb size={18} /></button>
                <button 
                    onClick={() => setPreviewPanelOpen(!previewPanelOpen)} 
                    className="hidden md:flex items-center gap-2 px-3 py-1.5 text-xs font-bold text-gray-400 hover:text-white transition-all border border-[#2A2A2A] rounded-lg hover:bg-[#2A2A2A]"
                >
                    <Eye size={16} />
                    <span>Preview</span>
                </button>
            </div>
        </div>
        <div className="px-4 py-2 border-b border-[#2A2A2A]">
            <ProgressBar progress={progress} lastSaved={lastSaved} />
        </div>
        
        {currentStep < 4 ? (<div className="p-4 flex-1"><div className={"p-4 border " + UI.BORDER + " rounded-lg " + UI.BG_SECONDARY + " shadow-inner"}>
            {currentStep === 0 && <Step0_DocType setState={setFlowState} nextStep={() => setCurrentStep(1)} />}
            {currentStep === 1 && <Step1_CareerInfo state={flowState} setState={setFlowState} nextStep={() => setCurrentStep(2)} onBack={() => setCurrentStep(0)} onSkip={() => setCurrentStep(4)} />}
            {currentStep === 2 && <Step2_SmartQuestions nextStep={() => setCurrentStep(3)} onBack={() => setCurrentStep(1)} />}
            {currentStep === 3 && <Step3_FormatRecommendation setResumeData={setResumeData} setStep={setCurrentStep} onBack={() => setCurrentStep(2)} />}
        </div></div>) : (
          <div className="flex-1 flex flex-col">
            {showDownload ? (
                <div className="p-4 space-y-6 animate-fadeIn">
                    <div className="p-4 bg-[#202020] border border-[#2A2A2A] rounded-lg">
                        <h3 className="font-bold text-[#EDEDED] mb-2 text-sm uppercase">Final review</h3>
                        <div className="grid grid-cols-2 md:grid-cols-3 gap-4">
                            {Object.keys(templates).map(key => (<TemplateThumbnail key={key} tKey={key} selected={templateKey === key} onClick={setTemplateKey} />))}
                        </div>
                    </div>
                    <SectionReorderPanel resumeData={resumeData} setResumeData={setResumeData} />
                    <div className="p-4 bg-[#202020] border border-[#2A2A2A] rounded-lg">
                        <h3 className="font-bold text-[#EDEDED] mb-2 text-sm uppercase">Typography</h3>
                        <div className="flex space-x-2">{[{ name: 'Modern', class: 'font-sans' }, { name: 'Classic', class: 'font-serif' }, { name: 'Mono', class: 'font-mono' }].map(f => <button key={f.class} onClick={() => setFont(f.class)} className={"flex-1 py-2 px-3 rounded-md text-sm font-medium border " + (font === f.class ? 'bg-[#FACC15] text-black border-[#FACC15]' : 'bg-[#191919] text-gray-400 border-[#2A2A2A]')}>{f.name}</button>)}</div>
                    </div>
                    
                    <div className="grid grid-cols-1 gap-3">
                        <button onClick={() => { setDownloadFormat('pdf'); setAdModalOpen(true); }} className="w-full py-3 rounded-lg bg-[#FACC15] text-black font-black flex items-center justify-center gap-2 transform transition-transform active:scale-95 shadow-xl">
                            <FileText size={20} /> Download PDF
                        </button>
                        <button onClick={() => { setDownloadFormat('docx'); setAdModalOpen(true); }} className="w-full py-3 rounded-lg border border-[#2A2A2A] bg-[#191919] text-white font-bold flex items-center justify-center gap-2 transform transition-transform active:scale-95">
                            <FileCode size={20} /> Download DOCX
                        </button>
                    </div>
                    
                    <button onClick={() => setShowDownload(false)} className="w-full py-2 text-gray-500 hover:text-white text-sm text-center">Back to editor</button>
                </div>
            ) : (
                <div className="p-4 flex-1 flex flex-col">
                    <div className="flex flex-wrap gap-2 mb-4">
                        {CurrentOrder.map(key => (<button key={key} onClick={() => setEditingSection(key)} className={"px-3 py-1.5 text-xs rounded-full font-bold border transition-colors " + (editingSection === key ? 'bg-[#FACC15] text-black border-[#FACC15]' : 'bg-[#191919] text-gray-400 border-[#2A2A2A] hover:border-gray-500')}>{sectionDefinitions[key].title}</button>))}
                    </div>
                    <div className="flex-1 p-4 border border-[#2A2A2A] rounded-lg bg-[#1a1a1a] shadow-inner overflow-y-auto">
                        <div className="flex items-center gap-2 mb-6 text-[#FACC15]"><Rocket size={16} /><h4 className="text-lg font-black uppercase text-white">{sectionDefinitions[editingSection].title}</h4></div>
                        {CurrentEditor && <CurrentEditor data={resumeData} setData={setResumeData} openExamples={openExamples} />}
                    </div>
                    <div className={`mt-6 flex justify-between pt-4 border-t border-[#2A2A2A]`}>
                        <button onClick={goToPreviousSection} className="text-gray-500 hover:text-white px-4">Back</button>
                        <button onClick={goToNextSection} className={"px-6 py-2 rounded font-black uppercase transition-all transform active:scale-95 " + (hasNextSection ? "bg-[#FACC15] text-black" : "bg-green-500 text-white shadow-[0_0_20px_rgba(34,197,94,0.3)]")}>{hasNextSection ? 'Next step' : 'Finish'}</button>
                    </div>
                </div>
            )}
          </div>
        )}
        <WebAppFooter />
      </div>
      <div className={"flex-1 p-4 lg:p-8 overflow-auto " + (previewPanelOpen ? 'block' : 'hidden') + " " + UI.BG_PRIMARY + " print:block"}>
        <div className="mx-auto w-full max-w-4xl min-h-[1100px] print:shadow-none bg-white shadow-2xl rounded-lg overflow-hidden">
          {currentStep >= 4 ? <ResumeViewer data={resumeData} templateKey={templateKey} font={font} /> : <div className="p-8 text-center text-gray-600 border border-dashed border-[#333] rounded-xl h-full flex flex-col justify-center items-center">Start filling details...</div>}
        </div>
      </div>
    </div>
  );
};

export default App;
