import React, { useMemo, useState, useEffect } from "react";
import { motion } from "framer-motion";
import { Check, X, Trophy, Home, Play, ChevronRight } from "lucide-react";

/*************************************************
 * iPhone 17 Pro Max — Mock + Islam Quiz (V3.2)
 *  - FIX build error: removed all escaped quotes (\") in JSX
 *  - Guard: runtime self-tests now also assert there is **no backslash** in any question/choice
 *  - UI: mobile-first, 2-column answers, improved home hero
 *************************************************/

/************** iPhone Frame (enhanced) **************/
function DeviceFrame({ children }: { children: React.ReactNode }) {
  const W = 430, H = 932; // logical points
  return (
    <div className="w-full flex items-start justify-center py-6">
      <div className="relative" style={{ width: W + 48, height: H + 48 }}>
        {/* Soft halo */}
        <div className="absolute -inset-6 rounded-[64px] bg-gradient-to-br from-emerald-400/15 via-cyan-400/10 to-purple-400/10 blur-2xl" />
        {/* Body */}
        <div className="absolute inset-0 rounded-[64px] bg-gradient-to-br from-neutral-900 to-neutral-800 border border-white/10 shadow-2xl" />
        {/* Bezel */}
        <div className="absolute inset-[14px] rounded-[50px] bg-black/90 ring-1 ring-white/10" />
        {/* Side buttons */}
        <div className="absolute -left-1 top-[130px] h-10 w-1 rounded-r bg-neutral-700" />
        <div className="absolute -left-1 top-[180px] h-16 w-1 rounded-r bg-neutral-700" />
        <div className="absolute -right-1 top-[200px] h-56 w-1 rounded-l bg-neutral-700" />
        {/* Screen */}
        <div className="absolute inset-[18px] rounded-[46px] overflow-hidden bg-[radial-gradient(900px_600px_at_-10%_-20%,#0b1220,#05060a_45%,#030308)]">
          {/* Dynamic Island */}
          <div className="absolute left-1/2 -translate-x-1/2 top-2 h-9 w-40 bg-black/90 rounded-full border border-black/60 shadow-inner" />
          {/* Status bar */}
          <div className="h-10 flex items-center justify-between px-4 text-xs text-white/90">
            <span>{new Date().toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" })}</span>
            <div className="flex items-center gap-1">
              <div className="h-3 w-5 border border-white/70 rounded-sm relative">
                <div className="absolute inset-[2px] bg-white" />
              </div>
              <div className="h-3 w-3 rounded-full bg-white" />
              <div className="h-3 w-3 rounded-full bg-white/70" />
            </div>
          </div>
          {/* App container */}
          <div className="h-[calc(100%-40px)] w-full overflow-y-auto">
            <div className="mx-auto px-3 pb-8" style={{ width: W }}>
              {children}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}

/************** Data Types **************/
export type Question = { q: string; choices: string[]; correct: number; note?: string };

/************** Question Bank (5×20 = 100) **************/
const BANK: Record<string, Question[]> = {
  "Piliers & Adoration": [
    { q: "Combien de piliers comporte l'Islam ?", choices: ["3", "4", "5", "6"], correct: 2, note: "Shahada, Salat, Zakat, Sawm, Hajj." },
    { q: "Quel est le premier pilier ?", choices: ["La prière", "La profession de foi", "Le jeûne", "Le pèlerinage"], correct: 1 },
    { q: "Combien de prières obligatoires par jour ?", choices: ["3", "4", "5", "6"], correct: 2 },
    { q: "Le jeûne obligatoire a lieu pendant…", choices: ["Rajab", "Ramadan", "Sha'ban", "Dhou al-Hijja"], correct: 1 },
    { q: "La zakat est…", choices: ["Un don volontaire", "Un impôt étatique", "Une aumône obligatoire", "Interdite"], correct: 2 },
    { q: "La prière du vendredi se nomme…", choices: ["Fajr", "Jumu'a", "Isha", "Witr"], correct: 1 },
    { q: "Combien de raka'at pour la prière du Maghrib ?", choices: ["2", "3", "4", "5"], correct: 1 },
    { q: "La direction de la prière s'appelle…", choices: ["Sunna", "Qibla", "Fiqh", "Sira"], correct: 1 },
    { q: "Le pèlerinage (Hajj) est obligatoire pour…", choices: ["Tous, sans condition", "Ceux qui en ont la capacité", "Uniquement les savants", "Uniquement les hommes"], correct: 1 },
    { q: "Quand s'acquitte la zakat al-fitr ?", choices: ["Début de Ramadan", "Milieu de Ramadan", "Fin de Ramadan", "Pendant le Hajj"], correct: 2 },
    { q: "Qu'est-ce que le wudu ?", choices: ["Aumône", "Ablutions", "Jeûne", "Prière nocturne"], correct: 1 },
    { q: "La prière nocturne surérogatoire est…", choices: ["Tarawih", "Tahajjud", "Witr", "Toutes"], correct: 3 },
    { q: "Quel mois est recommandé pour le jeûne en dehors de Ramadan ?", choices: ["Muharram", "Safar", "Rabi' al-awwal", "Djoumada"], correct: 0, note: "Le jeûne d'Achoura en Muharram est méritoire." },
    { q: "Le Adhan est…", choices: ["L'appel à la prière", "Une aumône", "Un sermon", "Un hadith"], correct: 0 },
    { q: "Combien de tours (tawaf) autour de la Ka'ba ?", choices: ["5", "6", "7", "8"], correct: 2 },
    { q: "La petite ablution rompue par…", choices: ["Sommeil profond", "Regard", "Soif", "Faim"], correct: 0 },
    { q: "La prière est invalide sans…", choices: ["Intention", "Chaussures", "Tapis", "Imam"], correct: 0 },
    { q: "La prière du coucher du soleil se nomme…", choices: ["Dhuhr", "Asr", "Maghrib", "Isha"], correct: 2 },
    { q: "Le Hajj se déroule durant…", choices: ["Ramadan", "Dhou al-Hijja", "Muharram", "Safar"], correct: 1 },
    { q: "L'ablution majeure s'appelle…", choices: ["Ghusl", "Tayammum", "Wudu", "Istinja"], correct: 0 }
  ],
  "Coran & Révélations": [
    { q: "Combien de sourates compte le Coran ?", choices: ["110", "112", "114", "116"], correct: 2 },
    { q: "La première révélation a eu lieu dans la grotte…", choices: ["Hira", "Thawr", "Uhud", "Badr"], correct: 0 },
    { q: "La nuit de la Destinée se nomme…", choices: ["Isra", "Mi'raj", "Laylat al-Qadr", "Arafah"], correct: 2 },
    { q: "La sourate d'ouverture est…", choices: ["Al-Ikhlas", "Al-Fatiha", "Al-Baqara", "An-Nas"], correct: 1 },
    { q: "Le Coran est révélé en langue…", choices: ["Arabe", "Hébreu", "Syriaque", "Persan"], correct: 0 },
    { q: "Combien de juz' (parties) ?", choices: ["20", "30", "40", "60"], correct: 1 },
    { q: "Quelle sourate est la plus longue ?", choices: ["Al-Baqara", "Al-Imran", "An-Nisa'", "Al-Ma'ida"], correct: 0 },
    { q: "Quelle sourate est la plus courte ?", choices: ["Al-Kawthar", "Al-Asr", "An-Nasr", "Al-Ikhlas"], correct: 0 },
    { q: "Le compagnon principal scribes du Coran fut…", choices: ["Zayd ibn Thabit", "Abu Hurayra", "Ibn Abbas", "Bilal"], correct: 0 },
    { q: "Le calife qui a standardisé le mushaf…", choices: ["Abu Bakr", "Umar", "Uthman", "Ali"], correct: 2 },
    { q: "La sourate 'Ikhlas' affirme…", choices: ["La miséricorde", "L'unicité d'Allah", "Le jeûne", "La prière"], correct: 1 },
    { q: "Le Coran fut révélé sur…", choices: ["23 ans", "10 ans", "33 ans", "5 ans"], correct: 0 },
    { q: "Le verset du Trône se trouve dans…", choices: ["An-Nisa'", "Al-Imran", "Al-Baqara", "Yasin"], correct: 2 },
    { q: "Le Prophète ﷺ était…", choices: ["Analfabète", "Scribe", "Chanteur", "Astronome"], correct: 0 },
    { q: "Combien de maqamat (lectures) célèbres ?", choices: ["4", "7", "10", "12"], correct: 2 },
    { q: "La compilation initiale post-Badr fut sous…", choices: ["Abu Bakr", "Uthman", "Umar", "Ali"], correct: 0 },
    { q: "La sourate qui protège de la magie selon la Sunna…", choices: ["Al-Falaq & An-Nas", "Al-Baqara", "Al-Kahf", "Ar-Rahman"], correct: 0 },
    { q: "Quelle sourate est recommandée le vendredi ?", choices: ["Al-Kahf", "Al-Jumu'a", "Ar-Rahman", "As-Saff"], correct: 0 },
    { q: "Le mot 'Qur'an' signifie…", choices: ["Récitation", "Écriture", "Chant", "Lecture silencieuse"], correct: 0 },
    { q: "Combien de ruku' approximatifs contient le Coran ?", choices: ["300", "540", "556", "600"], correct: 2 }
  ],
  "Prophètes": [
    { q: "Le premier des hommes est…", choices: ["Nouh", "Adam", "Ibrahim", "Idris"], correct: 1 },
    { q: "Quel prophète fut jeté dans le feu ?", choices: ["Ibrahim", "Moussa", "Issa", "Younous"], correct: 0 },
    { q: "Quel prophète a été avalé par un grand poisson ?", choices: ["Younous", "Ayyoub", "Yacoub", "Chouaïb"], correct: 0 },
    { q: "Le père d'Ismaïl est…", choices: ["Ya'qub", "Ibrahim", "Younous", "Ayoub"], correct: 1 },
    { q: "Le prophète de la patience exemplaire est…", choices: ["Ayoub", "Haroun", "Lut", "Salih"], correct: 0 },
    { q: "Le prophète à qui fut donné le Zabur…", choices: ["Dawoud", "Soulayman", "Idris", "Zakariya"], correct: 0 },
    { q: "Le prophète roi, maître du vent et des djinns…", choices: ["Soulayman", "Dawoud", "Youssef", "Moussa"], correct: 0 },
    { q: "Le prophète envoyé avec la Torah…", choices: ["Issa", "Moussa", "Dawoud", "Soulayman"], correct: 1 },
    { q: "Quel prophète a interprété des rêves en Égypte ?", choices: ["Youssef", "Ya'qub", "Is'haq", "Ishaq"], correct: 0 },
    { q: "Le peuple de 'Ad fut envoyé à…", choices: ["Hud", "Salih", "Chouaïb", "Younous"], correct: 0 },
    { q: "Quel prophète a parlé dès le berceau ?", choices: ["Issa", "Yahya", "Zakariya", "Moussa"], correct: 0 },
    { q: "Le prophète compagnon des poissons fut…", choices: ["Younous", "Ayyoub", "Yacoub", "Isa"], correct: 0 },
    { q: "Quelle était la ville de Lut ?", choices: ["Madyan", "Sodome", "Aïla", "Yathrib"], correct: 1 },
    { q: "Quel prophète fut sauvé du feu ?", choices: ["Ibrahim", "Moussa", "Issa", "Younous"], correct: 0 },
    { q: "Le prophète qui a bâti, avec Ismaïl, la Ka'ba…", choices: ["Ibrahim", "Nouh", "Moussa", "Dawoud"], correct: 0 },
    { q: "Quel prophète a reçu les Tables ?", choices: ["Moussa", "Haroun", "Issa", "Soulayman"], correct: 0 },
    { q: "Le prophète qui a régné sur un royaume prospère…", choices: ["Soulayman", "Youssef", "Dawoud", "Ibrahim"], correct: 0 },
    { q: "Le prophète dont le signe fut la guérison des malades…", choices: ["Issa", "Moussa", "Younous", "Ayyoub"], correct: 0 },
    { q: "Combien de prophètes sont cités nommément dans le Coran ?", choices: ["18", "24", "25", "28"], correct: 2 },
    { q: "Le dernier des prophètes est…", choices: ["Muhammad ﷺ", "Issa", "Moussa", "Ibrahim"], correct: 0 }
  ],
  "Vie du Prophète ﷺ": [
    { q: "La naissance du Prophète ﷺ a eu lieu à…", choices: ["Médine", "La Mecque", "Taïf", "Jérusalem"], correct: 1 },
    { q: "La première épouse du Prophète ﷺ fut…", choices: ["Aïcha", "Hafsa", "Khadija", "Fatima"], correct: 2 },
    { q: "La hijra s'est faite vers…", choices: ["Taïf", "Médine", "Syrie", "Abyssinie"], correct: 1 },
    { q: "La bataille de Badr eut lieu en l'an…", choices: ["1 H", "2 H", "3 H", "5 H"], correct: 1 },
    { q: "Le compagnon muezzin célèbre…", choices: ["Bilal", "Abu Hurayra", "Ibn Mas'ud", "Abu Bakr"], correct: 0 },
    { q: "Le surnom d'Aïcha est…", choices: ["Umm Salama", "As-Siddiqa", "Al-Farouq", "Dhat an-Nitaqayn"], correct: 1 },
    { q: "Le traité de Hudaybiya fut…", choices: ["Une bataille", "Un traité de paix", "Un sermon", "Un pèlerinage"], correct: 1 },
    { q: "L'année de l'éléphant correspond…", choices: ["À la révélation", "À la naissance du Prophète ﷺ", "À la hijra", "À Badr"], correct: 1 },
    { q: "Le cimetiere de Médine pour les martyrs…", choices: ["Baqi'", "Uhud", "Badr", "Quba"], correct: 1 },
    { q: "Le premier calife après le Prophète ﷺ…", choices: ["Umar", "Uthman", "Ali", "Abu Bakr"], correct: 3 },
    { q: "La mosquée construite à Médine en premier…", choices: ["Mosquée du Prophète", "Quba", "Qiblatayn", "Kuba"], correct: 1 },
    { q: "La victoire de la conquête de La Mecque fut en…", choices: ["6 H", "7 H", "8 H", "9 H"], correct: 2 },
    { q: "La bataille d'Uhud opposa…", choices: ["Médinois vs Abyssiniens", "Musulmans vs Quraysh", "Ansar vs Muhajirun", "Perses vs Romains"], correct: 1 },
    { q: "Le surnom d'Umar est…", choices: ["As-Siddiq", "Al-Farouq", "Dhou an-Nourayn", "Abu Turab"], correct: 1 },
    { q: "La mère d'Ibrahim (fils du Prophète ﷺ) est…", choices: ["Maria la Copte", "Safiyya", "Khadija", "Aïcha"], correct: 0 },
    { q: "Qui a accompagné le Prophète ﷺ dans la grotte de Thawr ?", choices: ["Ali", "Abu Bakr", "Umar", "Uthman"], correct: 1 },
    { q: "La première mosquée fondée à Médine fut…", choices: ["Quba", "Masjid an-Nabawi", "Qiblatayn", "Masjid al-Aqsa"], correct: 0 },
    { q: "La prière fut prescrite lors de…", choices: ["Badr", "Isra' et Mi'raj", "Hudaybiya", "Hijra"], correct: 1 },
    { q: "Le surnom d'Uthman est…", choices: ["As-Siddiq", "Al-Farouq", "Dhou an-Nourayn", "Abu Hurayra"], correct: 2 },
    { q: "Le Prophète ﷺ est décédé en…", choices: ["La Mecque", "Médine", "Taïf", "Jérusalem"], correct: 1 }
  ],
  "Comportement & Croyance": [
    { q: "La sincérité de l'intention se dit…", choices: ["Ikhlas", "Riya", "Takabbur", "Hasad"], correct: 0 },
    { q: "Le bon comportement (éthique) se dit…", choices: ["Akhlaq", "Fiqh", "Usul", "Tajwid"], correct: 0 },
    { q: "Dire du mal d'autrui en son absence…", choices: ["Ghiba", "Namima", "Sabr", "Shukr"], correct: 0 },
    { q: "La patience en arabe est…", choices: ["Shukr", "Taqwa", "Sabr", "Hilm"], correct: 2 },
    { q: "La gratitude se dit…", choices: ["Shukr", "Sabr", "Birr", "Ihsan"], correct: 0 },
    { q: "L'excellence dans l'adoration est…", choices: ["Iman", "Islam", "Ihsan", "Taqwa"], correct: 2 },
    { q: "La piété (crainte révérencielle) est…", choices: ["Taqwa", "Kufr", "Shirk", "Fitra"], correct: 0 },
    { q: "Associer à Allah une divinité s'appelle…", choices: ["Tawhid", "Shirk", "Sunnah", "Bid'a"], correct: 1 },
    { q: "La foi comporte…", choices: ["Parole et acte", "Parole seulement", "Acte seulement", "Aucun"], correct: 0 },
    { q: "Le meilleur des hommes en caractère est…", choices: ["Le plus riche", "Le plus éloquent", "Celui au meilleur comportement", "Le plus fort"], correct: 2 },
    { q: "Saluer en disant…", choices: ["Bonjour", "Salam", "Paix", "Shalom"], correct: 1 },
    { q: "Le voisin a droit à…", choices: ["Aucun droit", "Peu de droits", "Beaucoup de droits", "Uniquement si parent"], correct: 2 },
    { q: "La médisance est…", choices: ["Permise", "Déconseillée", "Interdite", "Obligatoire"], correct: 2 },
    { q: "Le repentir sincère inclut…", choices: ["Regret seulement", "Arrêt du péché et regret", "Donner en aumône", "Jeûner"], correct: 1 },
    { q: "La fraternité en Islam repose sur…", choices: ["La tribu", "La foi", "La langue", "La richesse"], correct: 1 },
    { q: "Honorer ses parents se dit…", choices: ["Birr al-walidayn", "Hajj", "Zakat", "Qiyam"], correct: 0 },
    { q: "L'hypocrisie (nifaq) est…", choices: ["Une qualité", "Un péché grave", "Neutre", "Obligatoire"], correct: 1 },
    { q: "La prière en groupe vaut…", choices: ["Moins", "Égal", "Plus de récompense", "Aucune"], correct: 2 },
    { q: "Le cœur se purifie par…", choices: ["L'ostentation", "Le rappel d'Allah", "La colère", "L'oubli"], correct: 1 },
    { q: "La meilleure parole est…", choices: ["La poésie", "La plaisanterie", "La parole d'Allah", "Le silence"], correct: 2 }
  ]
};

/************** UI Atoms **************/
function Glass({ children, className = "" }: any) {
  return (
    <div className={`rounded-2xl bg-white/5 backdrop-blur border border-white/10 shadow-lg ${className}`}>
      {children}
    </div>
  );
}

function PrimaryButton({ children, onClick, disabled }: any) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`w-full rounded-xl py-3 px-4 text-white font-semibold shadow transition active:scale-[0.98] ${disabled ? "bg-gray-500/60" : "bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500"}`}
    >
      {children}
    </button>
  );
}

function OutlineButton({ children, onClick }: any) {
  return (
    <button onClick={onClick} className="w-full rounded-xl py-3 px-4 border border-white/20 text-white/90 font-medium active:scale-[0.98]">
      {children}
    </button>
  );
}

/************** Screens **************/
function HomeScreen({ start }: { start: (cat: string) => void }) {
  const cats = Object.keys(BANK);
  const [selected, setSelected] = useState<string>(cats[0]);
  const totalQ = useMemo(() => Object.values(BANK).reduce((s, a) => s + a.length, 0), []);

  return (
    <div className="text-white">
      {/* Hero */}
      <div className="pt-5 pb-6">
        <div className="relative overflow-hidden rounded-3xl border border-white/10 shadow-lg">
          <div className="absolute inset-0 bg-[conic-gradient(at_10%_10%,#10b98144,#06b6d444,#6366f144,#10b98144)] blur-2xl" />
          <div className="relative p-6">
            <div className="flex items-center justify-between">
              <div>
                <div className="text-3xl font-extrabold tracking-tight">Quiz Islam</div>
                <div className="text-white/80 text-sm max-w-[26ch]">Apprends et teste-toi — qu'Allah t'accorde la science bénéfique.</div>
              </div>
              <div className="hidden sm:block h-12 w-12 rounded-2xl bg-white/10 border border-white/10 backdrop-blur flex items-center justify-center">
                <Play className="h-6 w-6"/>
              </div>
            </div>
            <div className="mt-4 flex items-center gap-2 text-[11px]">
              <span className="px-2 py-1 rounded-full bg-white/10 border border-white/10">📚 {totalQ} questions</span>
              <span className="px-2 py-1 rounded-full bg-white/10 border border-white/10">🗂️ {cats.length} catégories</span>
              <span className="px-2 py-1 rounded-full bg-white/10 border border-white/10">📱 Mobile</span>
            </div>
          </div>
        </div>
      </div>

      {/* Category quick-pills */}
      <div className="mb-3 overflow-x-auto no-scrollbar -mx-3 px-3">
        <div className="flex gap-2 min-w-max">
          {cats.map(c => (
            <button key={c} onClick={()=>setSelected(c)} className={`px-3 py-2 rounded-full border text-sm whitespace-nowrap ${selected===c?"border-emerald-400 bg-emerald-400/10":"border-white/15 bg-white/5 hover:bg-white/10"}`}>
              {c}
            </button>
          ))}
        </div>
      </div>

      {/* Category cards */}
      <div className="grid grid-cols-1 gap-3">
        {cats.map(c => (
          <Glass key={c} className={`p-4 ${selected===c?"ring-1 ring-emerald-400/60 bg-white/10":""}`}>
            <label className="flex items-center gap-3 cursor-pointer" onClick={()=>setSelected(c)}>
              <input type="radio" name="cat" className="accent-emerald-500" checked={selected===c} onChange={()=>setSelected(c)} />
              <div className="flex-1">
                <div className="font-semibold">{c}</div>
                <div className="text-xs text-white/60">{BANK[c].length} questions</div>
              </div>
              <ChevronRight className="h-5 w-5 text-white/50"/>
            </label>
          </Glass>
        ))}
      </div>

      <div className="h-5" />
      <PrimaryButton onClick={() => start(selected)}>
        <div className="flex items-center justify-center gap-2"><Play className="h-4 w-4"/> Commencer le quiz</div>
      </PrimaryButton>
    </div>
  );
}

function Progress({ i, total }: { i: number; total: number }) {
  const pct = Math.round(((i + 1) / total) * 100);
  return (
    <div className="w-full">
      <div className="flex items-center justify-between text-xs text-white/70 mb-1">
        <span>Question {i + 1} / {total}</span>
        <span>{pct}%</span>
      </div>
      <div className="h-2 rounded-full bg-white/10 overflow-hidden">
        <motion.div
          layout
          initial={{ width: 0 }}
          animate={{ width: `${pct}%` }}
          transition={{ type: "spring", stiffness: 120, damping: 20 }}
          className="h-full bg-gradient-to-r from-emerald-500 to-teal-500"
        />
      </div>
    </div>
  );
}

function QuizScreen({ cat, done }: { cat: string; done: (score: number, answers: number[]) => void }) {
  const qs = BANK[cat];
  const [i, setI] = useState(0);
  const [answers, setAnswers] = useState<number[]>(Array(qs.length).fill(-1));
  const [locked, setLocked] = useState(false);

  const q = qs[i];

  function choose(idx: number) {
    if (locked) return;
    const next = answers.slice();
    next[i] = idx;
    setAnswers(next);
    setLocked(true);
  }

  function next() {
    if (i < qs.length - 1) {
      setI(i + 1);
      setLocked(false);
    } else {
      const score = answers.reduce((s, a, k) => s + (a === qs[k].correct ? 1 : 0), 0);
      done(score, answers);
    }
  }

  const picked = answers[i];
  const base = "text-left rounded-xl px-3 py-3 border transition active:scale-[0.99] h-full";

  return (
    <div className="text-white">
      <div className="flex items-center justify-between mb-3">
        <div className="font-semibold">{cat}</div>
        <div className="text-xs text-white/60">{qs.length} questions</div>
      </div>

      <Progress i={i} total={qs.length} />

      <div className="h-4" />
      <Glass className="p-4">
        <div className="text-base font-semibold mb-3 leading-snug">{q.q}</div>
        <div className="grid grid-cols-2 gap-2">
          {q.choices.map((c, idx) => {
            const state = picked;
            const isPicked = state === idx;
            const isCorrect = idx === q.correct && state !== -1;
            const color = state === -1 ? "border-white/15 hover:border-white/30" : isCorrect ? "border-emerald-500/90 bg-emerald-500/10" : isPicked ? "border-red-500/80 bg-red-500/10" : "border-white/10 opacity-60";
            return (
              <motion.button
                key={idx}
                onClick={() => choose(idx)}
                className={`${base} ${color}`}
                whileTap={{ scale: 0.985 }}
                layout
              >
                <div className="flex items-center gap-3">
                  <div className={`h-6 w-6 rounded-full border flex items-center justify-center ${isCorrect?"border-emerald-500 text-emerald-400": isPicked?"border-red-500 text-red-400":"border-white/30 text-white/40"}`}>
                    {isCorrect ? <Check className="h-4 w-4"/> : isPicked ? <X className="h-4 w-4"/> : null}
                  </div>
                  <div className="font-medium leading-snug">{c}</div>
                </div>
              </motion.button>
            );
          })}
        </div>
        {q.note && picked !== -1 && (
          <div className="mt-3 text-sm text-white/80">
            <span className="font-semibold">Note :</span> {q.note}
          </div>
        )}
      </Glass>

      <div className="h-4" />
      <PrimaryButton onClick={next} disabled={picked === -1}>{i < qs.length - 1 ? "Suivant" : "Terminer"}</PrimaryButton>
      <div className="h-2" />
      <OutlineButton onClick={() => done(0, Array(qs.length).fill(-1))}>Quitter</OutlineButton>
    </div>
  );
}

function ConfettiRow() {
  return (
    <div className="grid grid-cols-12 gap-1 text-lg select-none">
      {Array.from({ length: 48 }).map((_, i) => (
        <motion.div key={i} initial={{ y: -10, opacity: 0 }} animate={{ y: 0, opacity: 1 }} transition={{ delay: i * 0.02 }}>
          {i % 3 === 0 ? "✨" : i % 3 === 1 ? "🟢" : "🟣"}
        </motion.div>
      ))}
    </div>
  );
}

function ResultScreen({ cat, score, answers, restart }: { cat: string; score: number; answers: number[]; restart: () => void }) {
  const total = BANK[cat].length;
  const ratio = score / total;
  const msg = useMemo(() => {
    if (ratio === 1) return "Excellence !";
    if (ratio >= 0.8) return "Très bien !";
    if (ratio >= 0.6) return "Bien — continue !";
    return "Bon début — persévère !";
  }, [ratio]);

  return (
    <div className="text-white">
      <div className="text-center py-4">
        <motion.div initial={{ scale: 0.9, opacity: 0 }} animate={{ scale: 1, opacity: 1 }} transition={{ type: "spring", stiffness: 120 }} className="inline-flex items-center gap-3 px-4 py-2 rounded-full bg-white/10 border border-white/10">
          <Trophy className="h-5 w-5 text-amber-300" />
          <div className="text-sm">Résultat</div>
        </motion.div>
        <div className="mt-3 text-6xl font-extrabold tracking-tight bg-gradient-to-r from-emerald-400 to-teal-400 bg-clip-text text-transparent">{Math.round(ratio*100)}%</div>
        <div className="text-white/80">{score} / {total} — {msg}</div>
      </div>

      <Glass className="p-4">
        <div className="font-semibold mb-2">Réponses</div>
        <ul className="space-y-2 text-sm">
          {BANK[cat].map((q, i) => (
            <li key={i} className="rounded-xl border border-white/10 p-3">
              <div className="font-medium mb-1">{q.q}</div>
              <div className="flex flex-wrap gap-2">
                {q.choices.map((c, idx) => (
                  <span key={idx} className={`px-2 py-1 rounded-full text-xs border ${idx===q.correct?"border-emerald-500 text-emerald-400":"border-white/10 text-white/70"} ${answers[i]===idx?"ring-1 ring-white/30":""}`}>
                    {c}
                  </span>
                ))}
              </div>
            </li>
          ))}
        </ul>
      </Glass>

      <div className="my-4"><ConfettiRow /></div>

      <PrimaryButton onClick={restart}>Rejouer</PrimaryButton>
    </div>
  );
}

/************** Self-tests (sanity checks) **************/
function validateBank(): string[] {
  const errors: string[] = [];
  const cats = Object.keys(BANK);
  if (cats.length !== 5) errors.push(`Expected 5 categories, got ${cats.length}`);
  let total = 0;
  for (const c of cats) {
    const qs = BANK[c];
    if (!Array.isArray(qs)) errors.push(`Category '${c}' is not an array`);
    if (qs.length !== 20) errors.push(`Category '${c}' should have 20 questions, has ${qs.length}`);
    total += qs.length;
    qs.forEach((q, i) => {
      if (!q || typeof q.q !== "string") errors.push(`${c}[${i}] missing 'q'`);
      if (!Array.isArray(q.choices) || q.choices.length !== 4) errors.push(`${c}[${i}] must have exactly 4 choices`);
      if (typeof q.correct !== "number" || q.correct < 0 || q.correct > 3) errors.push(`${c}[${i}] 'correct' index out of range`);
      // NEW: ensure no backslash to avoid \uXXXX parse hazards
      if (q.q.includes("\\")) errors.push(`${c}[${i}] contains backslash in question`);
      q.choices.forEach((ch, j) => { if (ch.includes("\\")) errors.push(`${c}[${i}].choices[${j}] contains backslash`); });
    });
  }
  if (total !== 100) errors.push(`Expected 100 total questions, got ${total}`);
  return errors;
}

/************** Root App **************/
function QuizIslamApp() {
  const [phase, setPhase] = useState<"home"|"quiz"|"result">("home");
  const [cat, setCat] = useState<string>("Piliers & Adoration");
  const [score, setScore] = useState(0);
  const [answers, setAnswers] = useState<number[]>([]);
  const [testErrors, setTestErrors] = useState<string[]>([]);

  useEffect(() => {
    const errs = validateBank();
    setTestErrors(errs);
    if (errs.length) console.error("BANK validation errors:", errs);
    else console.info("BANK validation passed ✔");
  }, []);

  return (
    <div className="min-h-full" style={{ colorScheme: "dark" }}>
      <div className="sticky top-0 z-10 -mx-3 mb-3 bg-gradient-to-r from-emerald-600 to-teal-600 text-white px-4 py-3 border-b border-white/10">
        <div className="flex items-center justify-between">
          <div className="text-lg font-bold">Quiz Islam</div>
          <div className="text-[10px] text-white/90">Mobile</div>
        </div>
        {testErrors.length > 0 && (
          <div className="mt-2 text-[11px] bg-red-500/20 border border-red-500/40 rounded px-2 py-1">
            Tests échoués: {testErrors.length} — vérifie la banque de questions (voir console)
          </div>
        )}
      </div>

      {phase === "home" && (
        <HomeScreen start={(c) => { setCat(c); setPhase("quiz"); }} />
      )}

      {phase === "quiz" && (
        <QuizScreen cat={cat} done={(s, a) => { setScore(s); setAnswers(a); setPhase("result"); }} />
      )}

      {phase === "result" && (
        <ResultScreen cat={cat} score={score} answers={answers} restart={() => setPhase("home")} />
      )}
    </div>
  );
}

/************** Export: App in Phone **************/
export default function DemoPhone() {
  return (
    <DeviceFrame>
      <QuizIslamApp />
    </DeviceFrame>
  );
}
