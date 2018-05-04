---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/03/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: dac04ed9a43e19d022720979c8f83aa2b4132f78
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
**Dokumentera senaste uppdatering**: 3 April 3:00 PM PST.

Senaste avslöjande av en [ny klass för CPU-säkerhetsrisker](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) spekulativ exekvering sida-kanal attacker som kallas har resulterat i frågor från kunder som vill ha mer tydlighets skull.  

Microsoft har distribuerats åtgärder för alla våra molntjänster. Den infrastruktur som kör Azure och isolerar kundens arbetsbelastningar från varandra är skyddad.  Detta innebär att andra kunder som körs på Azure inte kan angrepp ditt program med hjälp av dessa problem.

Dessutom Azure utökar användningen av [minne bevarar Underhåll](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) när det är möjligt, pausa den virtuella datorn i upp till 30 sekunder medan värden uppdateras eller den virtuella datorn flyttas till en värd som redan uppdaterade.  Bevara Underhåll ytterligare minne mindre påverkan för kunden och eliminerar behovet av omstarter.  Azure kommer att använda dessa metoder när du gör systemomfattande uppdateringar till värden.

> [!NOTE] 
> Sen februari 2018 Intel Corporation publicerade uppdaterade [mikrokod Revision vägledning](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf) statusen för sina mikrokod-versioner som förbättrar stabiliteten och skyddar mot de senaste säkerhetsrisker som visas av [Google projektet noll](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Ändringar gjorda av Azure [3 januari 2018](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) påverkas inte av Intels mikrokod uppdateringen. Microsoft placera redan starkt åtgärder som skyddar Azure-kunder från andra virtuella Azure-datorer.  
>
> Intels mikrokod adresser variant 2 Spectre ([CVE-2017-5715](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715) eller gren mål injection) att skydda mot attacker som endast är tillämpliga där du kör delade eller ej betrodda arbetsbelastningar i din virtuella dator på Azure. Våra tekniker testar stabiliteten för att minimera påverkan på prestanda för mikrokod innan du gör den tillgänglig för Azure-kunder.  Som ett fåtal kunder köra ej betrodda arbetsbelastningar inom deras virtuella datorer, behöver de flesta kunder inte aktivera den här funktionen ut en gång. 
>
> Den här sidan kommer att uppdateras när finns mer information.  






## <a name="keeping-your-operating-systems-up-to-date"></a>Uppdatera ditt operativsystem kontinuerligt

När en OS-uppdatering inte krävs för att isolera dina program som körs på Azure från andra kunder som körs på Azure, men det är alltid en bra idé att hålla OS-versioner som är uppdaterade. Januari 2018 och senare [säkerhet uppdateringar för Windows](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) innehåller åtgärder för dessa problem.

I följande erbjudanden följer våra rekommenderade åtgärder för att uppdatera operativsystemet: 

<table>
<tr>
<th>Erbjudande</th> <th>Rekommenderad åtgärd </th>
</tr>
<tr>
<td>Azure Cloud Services </td>  <td>Aktivera automatisk uppdatering eller se till att du kör den senaste gäst-OS.</td>
</tr>
<tr>
<td>Virtuella Azure Linux-datorer</td> <td>Installera uppdateringar från leverantören av operativsystemet när det är tillgängligt. </td>
</tr>
<tr>
<td>Windows Azure virtuella datorer </td> <td>Kontrollera att du kör ett program som stöds antivirusprogram innan du installerar uppdateringar av operativsystem. Kontakta din antivirusprogramleverantör information om kompatibilitet.<p> Installera den [januari insamling av säkerhet](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Andra Azure PaaS-tjänster</td> <td>Det finns ingen åtgärd krävs för kunder som använder dessa tjänster. Azure håller automatiskt OS-versioner aktuella. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Mer information om du använder icke betrodd kod 

Ingen ytterligare kunden åtgärd krävs om du inte kör icke betrodd kod. Om du tillåter kod som du inte litar (till exempel du tillåter en av dina kunder att överföra en binär eller kodstycke som du kör i molnet i ditt program), och sedan följande åtgärder vidtas.  


### <a name="windows"></a>Windows 
Om du använder Windows och värd icke betrodd kod, bör du även aktivera en Windows-funktion som kallas Kernel virtuell adress (KVA) skuggning som ger ytterligare skydd mot spekulativ exekvering sida-kanal säkerhetsproblem (specifikt för Variant 3 ska smälta, [CVE-2017-5754](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5754), eller falsk datainläsning cache). Den här funktionen är inaktiverad som standard och kan påverka prestanda om aktiverat. Följ [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) instruktioner för att aktivera skydd på servern. Om du använder Azure Cloud Services, kontrollera att du kör WA-GÄST-OS-5.15_201801-01 eller WA-GUEST-OS-4.50_201801-01 (tillgänglig från på 10 januari 2018) och aktivera registret nyckel via en startaktivitet.


### <a name="linux"></a>Linux
Om du kör Linux och värd icke betrodd kod kan uppdatera du även Linux till en nyare version som implementerar kernel sidan tabell isolering (KPTI) som särskiljer sidan-tabeller som används av kernel från de som tillhör användaren utrymme. Dessa åtgärder kräver en Linux OS-uppdatering och kan hämtas från leverantören av distributionsplatsen när det är tillgängligt. OS-providern kan berätta om skydd är aktiverade eller inaktiverade som standard.



## <a name="next-steps"></a>Nästa steg

Läs mer i [skydda Azure-kunder från CPU säkerhetsproblem](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
