import React, { useState } from 'react';
import { 
  Truck, 
  Ship, 
  Package, 
  Anchor, 
  ArrowRight, 
  ShieldCheck, 
  DollarSign, 
  AlertTriangle,
  Info,
  Menu,
  X,
  FileText,
  Globe,
  Warehouse
} from 'lucide-react';

/**
 * DATOS DE INCOTERMS 2020
 * Definición de responsabilidades, costos y riesgos.
 * * Escala de pasos logísticos (0 a 7):
 * 0: Embalaje / Verificación
 * 1: Carga en origen
 * 2: Transporte interior origen
 * 3: Trámites aduaneros exportación
 * 4: Maniobras en puerto/lugar de origen
 * 5: Transporte principal (Flete internacional)
 * 6: Seguro de mercancía
 * 7: Maniobras en destino / Descarga
 * 8: Trámites aduaneros importación
 * 9: Transporte interior destino
 * 10: Descarga final
 */

const LOGISTICS_STEPS = [
  { id: 0, label: "Embalaje", icon: Package },
  { id: 1, label: "Carga en Origen", icon: Warehouse },
  { id: 2, label: "Transporte Interior", icon: Truck },
  { id: 3, label: "Aduana Export", icon: FileText },
  { id: 4, label: "Puerto Origen", icon: Anchor },
  { id: 5, label: "Transporte Principal", icon: Globe }, // Ship or Plane
  { id: 6, label: "Puerto Destino", icon: Anchor },
  { id: 7, label: "Aduana Import", icon: FileText },
  { id: 8, label: "Transporte Destino", icon: Truck },
  { id: 9, label: "Descarga Final", icon: Warehouse },
];

const INCOTERMS_DATA = [
  // --- MULTIMODALES ---
  {
    code: "EXW",
    name: "Ex Works",
    subtitle: "En Fábrica",
    type: "multimodal",
    desc: "El vendedor entrega la mercancía en sus instalaciones. El comprador asume TODOS los costos y riesgos desde ahí.",
    sellerCostIndex: 0, // Seller pays up to step 0 (packaging only)
    sellerRiskIndex: 0, // Risk transfers at step 0
    insurance: "Comprador",
    color: "bg-slate-500",
    details: "Mínima obligación para el vendedor."
  },
  {
    code: "FCA",
    name: "Free Carrier",
    subtitle: "Franco Porteador",
    type: "multimodal",
    desc: "El vendedor entrega la mercancía al transportista designado por el comprador en un punto acordado. El vendedor despacha la exportación.",
    sellerCostIndex: 3, 
    sellerRiskIndex: 3, 
    insurance: "Comprador",
    color: "bg-blue-600",
    details: "Muy flexible. Puede ser en la fábrica del vendedor (él carga) o en otro lugar (él no descarga)."
  },
  {
    code: "CPT",
    name: "Carriage Paid To",
    subtitle: "Transporte Pagado Hasta",
    type: "multimodal",
    desc: "El vendedor paga el transporte hasta el destino, pero el riesgo se transfiere al entregar al primer transportista.",
    sellerCostIndex: 6, // Pays main carriage
    sellerRiskIndex: 3, // Risk transfers at origin carrier
    insurance: "Comprador",
    color: "bg-indigo-600",
    details: "Transferencia de riesgo y costo ocurren en lugares diferentes."
  },
  {
    code: "CIP",
    name: "Carriage & Insurance Paid To",
    subtitle: "Transporte y Seguro Pagados Hasta",
    type: "multimodal",
    desc: "Similar al CPT, pero el vendedor DEBE contratar un seguro con cobertura amplia (Cláusula A).",
    sellerCostIndex: 6,
    sellerRiskIndex: 3,
    insurance: "Vendedor (Obligatorio)",
    color: "bg-indigo-700",
    details: "Cambio clave 2020: Requiere mayor cobertura de seguro que CIF."
  },
  {
    code: "DAP",
    name: "Delivered at Place",
    subtitle: "Entregada en Lugar",
    type: "multimodal",
    desc: "El vendedor entrega la mercancía en el lugar de destino convenido, lista para la descarga.",
    sellerCostIndex: 8,
    sellerRiskIndex: 8,
    insurance: "Vendedor (Recomendado)",
    color: "bg-emerald-600",
    details: "El comprador paga la descarga y los impuestos de importación."
  },
  {
    code: "DPU",
    name: "Delivered at Place Unloaded",
    subtitle: "Entregada y Descargada",
    type: "multimodal",
    desc: "El vendedor asume los riesgos y costos hasta descargar la mercancía en el lugar de destino.",
    sellerCostIndex: 9, // Includes unloading
    sellerRiskIndex: 9,
    insurance: "Vendedor (Recomendado)",
    color: "bg-emerald-700",
    details: "Sustituye al DAT. Único término donde el vendedor descarga."
  },
  {
    code: "DDP",
    name: "Delivered Duty Paid",
    subtitle: "Entregada Derechos Pagados",
    type: "multimodal",
    desc: "El vendedor asume todo: transporte, riesgos e impuestos de importación en destino.",
    sellerCostIndex: 9, // Technically usually delivered ready for unloading, but seller pays customs
    sellerRiskIndex: 8, // Risk usually at arrival
    customsSeller: true, // Special flag for import customs
    insurance: "Vendedor (Recomendado)",
    color: "bg-emerald-800",
    details: "Máxima obligación para el vendedor. Opuesto a EXW."
  },

  // --- MARÍTIMOS ---
  {
    code: "FAS",
    name: "Free Alongside Ship",
    subtitle: "Franco al Costado del Buque",
    type: "maritime",
    desc: "El vendedor entrega la mercancía al costado del buque en el puerto de embarque convenido.",
    sellerCostIndex: 3, // Export customs cleared, delivered alongside
    sellerRiskIndex: 3,
    insurance: "Comprador",
    color: "bg-cyan-600",
    details: "Uso común para graneles y carga no contenerizada."
  },
  {
    code: "FOB",
    name: "Free On Board",
    subtitle: "Franco a Bordo",
    type: "maritime",
    desc: "El vendedor entrega la mercancía a bordo del buque. El riesgo se transmite una vez estibado.",
    sellerCostIndex: 4, // Loaded on ship
    sellerRiskIndex: 4,
    insurance: "Comprador",
    color: "bg-cyan-700",
    details: "No recomendado para contenedores (usar FCA). Clásico marítimo."
  },
  {
    code: "CFR",
    name: "Cost and Freight",
    subtitle: "Costo y Flete",
    type: "maritime",
    desc: "El vendedor paga el flete hasta el puerto de destino. El riesgo se transfiere al cargar en origen.",
    sellerCostIndex: 6, // Pays to destination port
    sellerRiskIndex: 4, // Risk at origin loading
    insurance: "Comprador",
    color: "bg-sky-600",
    details: "Solo marítimo. Riesgo y costo en puntos distintos."
  },
  {
    code: "CIF",
    name: "Cost, Insurance & Freight",
    subtitle: "Costo, Seguro y Flete",
    type: "maritime",
    desc: "El vendedor paga transporte y seguro mínimo hasta destino. El riesgo pasa en origen.",
    sellerCostIndex: 6,
    sellerRiskIndex: 4,
    insurance: "Vendedor (Cobertura mínima C)",
    color: "bg-sky-700",
    details: "Histórico. Seguro solo requiere cobertura básica (Cláusula C)."
  }
];

// Componente para la barra de progreso de la cadena de suministro
const SupplyChainVisualizer = ({ term }) => {
  return (
    <div className="w-full py-6 overflow-x-auto">
      <div className="min-w-[700px] px-4">
        {/* Etiquetas Superiores */}
        <div className="flex justify-between text-xs font-bold text-gray-400 mb-2 uppercase tracking-wider">
          <span>Origen (Vendedor)</span>
          <span>Transporte Principal</span>
          <span>Destino (Comprador)</span>
        </div>

        {/* Línea de Costos */}
        <div className="mb-6">
          <div className="flex items-center mb-1">
            <div className="w-24 text-xs font-bold text-indigo-600 uppercase">Costos</div>
            <div className="flex-1 relative h-4 bg-gray-200 rounded-full overflow-hidden flex">
              {LOGISTICS_STEPS.map((step, index) => {
                // Logic: Does seller pay for this step?
                let isSeller = index <= term.sellerCostIndex;
                if (term.code === "DDP" && step.id === 7) isSeller = true; // DDP pays import customs
                if (term.code === "DPU" && step.id === 9) isSeller = true; // DPU pays unloading
                
                return (
                  <div 
                    key={step.id} 
                    className={`h-full flex-1 border-r border-white/20 transition-all duration-500 ${isSeller ? 'bg-indigo-600' : 'bg-amber-400'}`}
                    title={`${step.label}: ${isSeller ? 'Vendedor' : 'Comprador'}`}
                  />
                );
              })}
            </div>
            <div className="w-24 text-right text-xs font-bold text-amber-500 uppercase">Comprador</div>
          </div>
        </div>

        {/* Línea de Riesgos */}
        <div className="mb-8">
          <div className="flex items-center mb-1">
            <div className="w-24 text-xs font-bold text-indigo-600 uppercase">Riesgos</div>
            <div className="flex-1 relative h-4 bg-gray-200 rounded-full overflow-hidden flex">
              {LOGISTICS_STEPS.map((step, index) => {
                let isSeller = index <= term.sellerRiskIndex;
                // Special edge cases can be handled here
                return (
                  <div 
                    key={step.id} 
                    className={`h-full flex-1 border-r border-white/20 transition-all duration-500 ${isSeller ? 'bg-indigo-600' : 'bg-amber-400'}`}
                    title={`${step.label}: ${isSeller ? 'Vendedor' : 'Comprador'}`}
                  />
                );
              })}
            </div>
            <div className="w-24 text-right text-xs font-bold text-amber-500 uppercase">Comprador</div>
          </div>
        </div>

        {/* Iconos de la cadena logística */}
        <div className="flex justify-between relative pl-24 pr-24">
           {/* Line connecting icons */}
           <div className="absolute top-1/2 left-24 right-24 h-0.5 bg-gray-200 -z-10"></div>
           
           {LOGISTICS_STEPS.map((step, index) => {
             const isRiskTransfer = index === term.sellerRiskIndex;
             const isCostTransfer = index === term.sellerCostIndex;
             
             return (
               <div key={step.id} className="flex flex-col items-center group relative cursor-pointer">
                  <div className={`
                    w-10 h-10 rounded-full flex items-center justify-center border-2 z-10 bg-white transition-colors duration-300
                    ${isRiskTransfer ? 'border-red-500 shadow-lg shadow-red-100' : 'border-gray-300 group-hover:border-indigo-400'}
                  `}>
                    <step.icon size={16} className={`${isRiskTransfer ? 'text-red-500' : 'text-gray-500'}`} />
                  </div>
                  
                  {/* Etiqueta del paso */}
                  <span className="absolute -bottom-8 w-24 text-center text-[10px] font-medium text-gray-500 leading-tight">
                    {step.label}
                  </span>

                  {/* Indicador de transferencia de riesgo */}
                  {isRiskTransfer && (
                    <div className="absolute -top-8 bg-red-500 text-white text-[10px] py-0.5 px-2 rounded-full whitespace-nowrap animate-bounce">
                      Transferencia de Riesgo
                    </div>
                  )}
               </div>
             );
           })}
        </div>
      </div>
    </div>
  );
};

const ResponsibilityRow = ({ label, isSeller, icon: Icon }) => (
  <div className="flex items-center justify-between py-3 border-b border-gray-100 last:border-0 hover:bg-gray-50 px-2 rounded transition-colors">
    <div className="flex items-center gap-3">
      <div className={`p-2 rounded-lg ${isSeller ? 'bg-indigo-50 text-indigo-600' : 'bg-amber-50 text-amber-600'}`}>
        <Icon size={18} />
      </div>
      <span className="text-sm font-medium text-gray-700">{label}</span>
    </div>
    <div className="flex items-center gap-2">
      <span className={`text-xs font-bold px-2 py-1 rounded ${isSeller ? 'bg-indigo-100 text-indigo-700' : 'bg-amber-100 text-amber-700'}`}>
        {isSeller ? 'Vendedor' : 'Comprador'}
      </span>
    </div>
  </div>
);

export default function IncotermsApp() {
  const [selectedCode, setSelectedCode] = useState("FCA");
  const [isSidebarOpen, setIsSidebarOpen] = useState(false);

  const selectedTerm = INCOTERMS_DATA.find(t => t.code === selectedCode);

  // Funciones auxiliares para calcular responsabilidades
  const checkResp = (stepIndex, term) => {
     // Special cases
     if (term.code === "DDP" && stepIndex === 7) return true; // Customs import
     return stepIndex <= term.sellerCostIndex;
  };

  const isDDP = selectedCode === "DDP";
  
  return (
    <div className="flex h-screen bg-gray-50 font-sans text-slate-800 overflow-hidden">
      
      {/* Mobile Sidebar Overlay */}
      {isSidebarOpen && (
        <div 
          className="fixed inset-0 bg-black/50 z-20 lg:hidden"
          onClick={() => setIsSidebarOpen(false)}
        />
      )}

      {/* Sidebar Navigation */}
      <aside className={`
        fixed lg:static inset-y-0 left-0 z-30 w-72 bg-white border-r border-gray-200 transform transition-transform duration-300 ease-in-out flex flex-col
        ${isSidebarOpen ? 'translate-x-0' : '-translate-x-full lg:translate-x-0'}
      `}>
        <div className="p-6 border-b border-gray-100 flex justify-between items-center">
          <div className="flex items-center gap-2 text-indigo-700">
            <Globe className="w-6 h-6" />
            <h1 className="text-xl font-bold tracking-tight">Incoterms<sup className="text-xs">®</sup> 2020</h1>
          </div>
          <button onClick={() => setIsSidebarOpen(false)} className="lg:hidden text-gray-400">
            <X size={24} />
          </button>
        </div>

        <div className="flex-1 overflow-y-auto p-4 space-y-6">
          
          <div>
            <h3 className="text-xs font-bold text-gray-400 uppercase tracking-wider mb-3 px-2">Cualquier Transporte</h3>
            <div className="space-y-1">
              {INCOTERMS_DATA.filter(t => t.type === 'multimodal').map(term => (
                <button
                  key={term.code}
                  onClick={() => { setSelectedCode(term.code); setIsSidebarOpen(false); }}
                  className={`w-full flex items-center justify-between px-3 py-2.5 rounded-lg text-sm font-medium transition-all ${
                    selectedCode === term.code 
                      ? 'bg-indigo-50 text-indigo-700 shadow-sm border border-indigo-100' 
                      : 'text-gray-600 hover:bg-gray-50 hover:text-indigo-600'
                  }`}
                >
                  <div className="flex items-center gap-3">
                    <span className={`w-1.5 h-1.5 rounded-full ${selectedCode === term.code ? 'bg-indigo-500' : 'bg-gray-300'}`}></span>
                    {term.code}
                  </div>
                  <span className="text-[10px] opacity-60 font-normal truncate max-w-[120px]">{term.name}</span>
                </button>
              ))}
            </div>
          </div>

          <div>
            <h3 className="text-xs font-bold text-gray-400 uppercase tracking-wider mb-3 px-2">Marítimo y Vías Navegables</h3>
            <div className="space-y-1">
              {INCOTERMS_DATA.filter(t => t.type === 'maritime').map(term => (
                <button
                  key={term.code}
                  onClick={() => { setSelectedCode(term.code); setIsSidebarOpen(false); }}
                  className={`w-full flex items-center justify-between px-3 py-2.5 rounded-lg text-sm font-medium transition-all ${
                    selectedCode === term.code 
                      ? 'bg-sky-50 text-sky-700 shadow-sm border border-sky-100' 
                      : 'text-gray-600 hover:bg-gray-50 hover:text-sky-600'
                  }`}
                >
                  <div className="flex items-center gap-3">
                    <Ship size={14} className={selectedCode === term.code ? 'text-sky-500' : 'text-gray-300'} />
                    {term.code}
                  </div>
                  <span className="text-[10px] opacity-60 font-normal truncate max-w-[120px]">{term.name}</span>
                </button>
              ))}
            </div>
          </div>

        </div>

        <div className="p-4 border-t border-gray-100 text-xs text-center text-gray-400">
          Manual Educativo v1.0
        </div>
      </aside>

      {/* Main Content */}
      <main className="flex-1 flex flex-col overflow-hidden relative">
        {/* Mobile Header */}
        <div className="lg:hidden p-4 bg-white border-b border-gray-200 flex items-center justify-between">
          <span className="font-bold text-lg text-indigo-900">{selectedCode}</span>
          <button onClick={() => setIsSidebarOpen(true)} className="p-2 text-gray-600">
            <Menu size={24} />
          </button>
        </div>

        <div className="flex-1 overflow-y-auto p-4 lg:p-8">
          <div className="max-w-5xl mx-auto space-y-6">
            
            {/* Header Card */}
            <div className="bg-white rounded-2xl shadow-sm border border-gray-100 p-6 lg:p-8 relative overflow-hidden">
              <div className={`absolute top-0 right-0 w-32 h-32 bg-gradient-to-br from-transparent to-current opacity-10 rounded-bl-full pointer-events-none ${selectedTerm.type === 'maritime' ? 'text-sky-500' : 'text-indigo-500'}`}></div>
              
              <div className="flex flex-col md:flex-row md:items-start justify-between gap-4 relative z-10">
                <div>
                  <div className="flex items-center gap-3 mb-2">
                     <span className={`text-4xl lg:text-5xl font-extrabold tracking-tight ${selectedTerm.type === 'maritime' ? 'text-sky-700' : 'text-indigo-700'}`}>
                       {selectedTerm.code}
                     </span>
                     {selectedTerm.type === 'maritime' && <Ship className="text-sky-400 mt-2" size={32} />}
                     {selectedTerm.type === 'multimodal' && <Truck className="text-indigo-400 mt-2" size={32} />}
                  </div>
                  <h2 className="text-xl lg:text-2xl font-medium text-gray-800">{selectedTerm.name}</h2>
                  <p className="text-gray-500 text-lg">{selectedTerm.subtitle}</p>
                </div>
                
                <div className="md:max-w-md bg-gray-50 p-4 rounded-xl border border-gray-100">
                  <div className="flex items-start gap-2">
                    <Info className="w-5 h-5 text-indigo-500 mt-0.5 shrink-0" />
                    <p className="text-sm text-gray-600 leading-relaxed">
                      {selectedTerm.desc}
                    </p>
                  </div>
                </div>
              </div>
            </div>

            {/* Visualizer Section */}
            <div className="bg-white rounded-2xl shadow-lg shadow-gray-200/50 border border-gray-100 p-6 overflow-hidden">
              <h3 className="text-lg font-bold text-gray-800 mb-6 flex items-center gap-2">
                <ArrowRight className="w-5 h-5 text-indigo-500" />
                Cadena de Transferencia
              </h3>
              
              <SupplyChainVisualizer term={selectedTerm} />
              
              <div className="mt-8 grid grid-cols-1 md:grid-cols-2 gap-4 text-sm bg-gray-50 p-4 rounded-xl">
                 <div className="flex items-center gap-2">
                    <div className="w-3 h-3 bg-indigo-600 rounded-full"></div>
                    <span className="text-gray-600">
                      <strong>Azul:</strong> Obligación del Vendedor
                    </span>
                 </div>
                 <div className="flex items-center gap-2">
                    <div className="w-3 h-3 bg-amber-400 rounded-full"></div>
                    <span className="text-gray-600">
                      <strong>Amarillo:</strong> Obligación del Comprador
                    </span>
                 </div>
                 <div className="col-span-1 md:col-span-2 text-xs text-gray-500 mt-2 border-t border-gray-200 pt-2">
                   <span className="text-red-500 font-bold">* Icono rojo</span> indica el punto exacto donde el riesgo de daño o pérdida pasa del vendedor al comprador.
                 </div>
              </div>
            </div>

            {/* Detailed Responsibility Table */}
            <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
              
              {/* Main List */}
              <div className="lg:col-span-2 bg-white rounded-2xl shadow-sm border border-gray-100 p-6">
                <h3 className="text-lg font-bold text-gray-800 mb-4 flex items-center gap-2">
                   <FileText className="w-5 h-5 text-gray-400" />
                   Obligaciones Específicas
                </h3>
                
                <div className="space-y-1">
                  <ResponsibilityRow 
                    label="Embalaje y Verificación" 
                    icon={Package} 
                    isSeller={true} 
                  />
                  <ResponsibilityRow 
                    label="Carga en Instalaciones (Origen)" 
                    icon={Warehouse} 
                    isSeller={selectedCode !== 'EXW'} // Seller loads in all except EXW
                  />
                  <ResponsibilityRow 
                    label="Trámites Aduana Exportación" 
                    icon={FileText} 
                    isSeller={selectedCode !== 'EXW'} 
                  />
                  <ResponsibilityRow 
                    label="Transporte Principal (Flete)" 
                    icon={selectedTerm.type === 'maritime' ? Ship : Globe} 
                    isSeller={['CPT','CIP','DAP','DPU','DDP','CFR','CIF'].includes(selectedCode)} 
                  />
                  <ResponsibilityRow 
                    label="Seguro de Mercancía" 
                    icon={ShieldCheck} 
                    isSeller={['CIP', 'CIF'].includes(selectedCode)} // Mandatory insurance
                  />
                  <ResponsibilityRow 
                    label="Trámites Aduana Importación" 
                    icon={FileText} 
                    isSeller={isDDP} 
                  />
                  <ResponsibilityRow 
                    label="Descarga en Destino" 
                    icon={Warehouse} 
                    isSeller={selectedCode === 'DPU'} 
                  />
                </div>
              </div>

              {/* Info Cards */}
              <div className="space-y-4">
                {/* Insurance Card */}
                <div className={`p-5 rounded-2xl border ${
                  ['CIP', 'CIF'].includes(selectedCode) 
                    ? 'bg-blue-50 border-blue-100' 
                    : 'bg-white border-gray-100'
                }`}>
                  <div className="flex items-center gap-2 mb-2">
                    <ShieldCheck className={`w-5 h-5 ${['CIP', 'CIF'].includes(selectedCode) ? 'text-blue-600' : 'text-gray-400'}`} />
                    <h4 className="font-bold text-gray-700">Seguro</h4>
                  </div>
                  <p className="text-sm text-gray-600">
                    {selectedTerm.insurance === 'Comprador' 
                      ? "No es obligación del vendedor, pero el riesgo existe. Se recomienda al comprador contratarlo."
                      : selectedTerm.insurance.includes("Vendedor")
                      ? "El vendedor está OBLIGADO a contratar el seguro y entregar la póliza al comprador."
                      : selectedTerm.insurance
                    }
                  </p>
                </div>

                {/* Key Note Card */}
                <div className="bg-amber-50 p-5 rounded-2xl border border-amber-100">
                  <div className="flex items-center gap-2 mb-2">
                    <AlertTriangle className="w-5 h-5 text-amber-600" />
                    <h4 className="font-bold text-amber-800">Punto Crítico</h4>
                  </div>
                  <p className="text-sm text-amber-700 leading-snug">
                    {selectedTerm.details}
                  </p>
                </div>

                {/* Cost Summary Visual */}
                 <div className="bg-white p-5 rounded-2xl border border-gray-100">
                    <div className="flex items-center gap-2 mb-4">
                      <DollarSign className="w-5 h-5 text-emerald-600" />
                      <h4 className="font-bold text-gray-700">Resumen Costos</h4>
                    </div>
                    <div className="space-y-3">
                      <div>
                        <div className="flex justify-between text-xs mb-1 text-gray-500">
                          <span>Vendedor</span>
                          <span>{Math.round(((selectedTerm.sellerCostIndex + 1) / 10) * 100)}%</span>
                        </div>
                        <div className="w-full bg-gray-100 rounded-full h-2">
                          <div 
                            className="bg-indigo-600 h-2 rounded-full transition-all duration-700" 
                            style={{ width: `${((selectedTerm.sellerCostIndex + 1) / 10) * 100}%` }}
                          ></div>
                        </div>
                      </div>
                      <div>
                        <div className="flex justify-between text-xs mb-1 text-gray-500">
                          <span>Comprador</span>
                          <span>{100 - Math.round(((selectedTerm.sellerCostIndex + 1) / 10) * 100)}%</span>
                        </div>
                        <div className="w-full bg-gray-100 rounded-full h-2">
                          <div 
                            className="bg-amber-400 h-2 rounded-full transition-all duration-700" 
                            style={{ width: `${100 - (((selectedTerm.sellerCostIndex + 1) / 10) * 100)}%` }}
                          ></div>
                        </div>
                      </div>
                    </div>
                 </div>

              </div>
            </div>

          </div>
        </div>
      </main>
    </div>
  );
}
