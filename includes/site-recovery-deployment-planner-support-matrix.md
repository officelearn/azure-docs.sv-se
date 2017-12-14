# <a name="support-matrix"></a>Supportmatris

| | **VMware till Azure** |**Hyper-V till Azure**|**Azure till Azure**|**Hyper-V till sekundär plats**|**VMware till sekundär plats**
--|--|--|--|--|--
Scenarier som stöds |Ja|Ja|Nej|Ja*|Nej
Version som stöds | vCenter 6.5, 6.0 eller 5.5| Windows Server 2016, Windows Server 2012 R2 | Ej tillämpligt |Windows Server 2016, Windows Server 2012 R2|Ej tillämpligt
Konfigurationer som stöds|vCenter/ESXi| Hyper-V-kluster, Hyper-V-värd|Ej tillämpligt|Hyper-V-kluster, Hyper-V-värd|Ej tillämpligt|
Antalet servrar som kan ska profileras startas per kör instans av Azure Site Recovery-distribution Planner |Enkel (virtuella datorer som hör till en vCenter-Server eller en ESXi-servern kan ska profileras startas i taget)|Flera (virtuella datorer över flera värdar eller värdkluster kan vara profil i taget)| Ej tillämpligt |Flera (virtuella datorer över flera värdar eller värdkluster kan vara profil i taget)| Ej tillämpligt

* Verktyget är främst för Hyper-V till Azure katastrofåterställning. För Hyper-V till sekundär plats katastrofåterställning, kan den bara användas för att förstå källa sida rekommendationer som krävs nätverksbandbredd, krävs ledigt lagringsutrymme på varje källservrar Hyper-V och den inledande replikeringen batchbearbetning siffror och batch definitioner.  Ignorera Azure rekommendationer och kostnader i rapporten. Dessutom gäller hämta genomströmning åtgärden inte för Hyper-V till sekundär plats för katastrofåterställning.
