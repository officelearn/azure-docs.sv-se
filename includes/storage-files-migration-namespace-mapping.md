---
title: Mappa en mappstruktur till en Azure File Sync-topologi
description: Mappa en befintlig fil-och mappstruktur till Azure-filresurser för användning med Azure File Sync. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209563"
---
I det här steget ska du utvärdera hur många Azure-filresurser du behöver. En enda Windows Server (eller ett kluster) kan synkronisera upp till 30 Azure-filresurser.

Du kan ha fler mappar på din StorSimple som du för närvarande delar ut lokalt som en SMB-resurs till dina användare och appar. Det enklaste sättet är att Envision för en lokal resurs för att mappa 1:1 till en Azure-filresurs. Om det här talet är så litet, vilket innebär att det är mindre än 30 för en enda Windows Server, eller om du planerar att ha två Windows-servrar (60) och så vidare, så rekommenderas en 1:1-mappning.

Om du har fler resurser än 30 är det ofta onödigt att mappa en lokal resurs 1:1 till en Azure-filresurs.
Överväg följande alternativ:

#### <a name="share-grouping"></a>Dela gruppering

Om din personal avdelning till exempel har totalt 15 resurser kan du överväga att lagra alla HR-data i en enda Azure-filresurs. Att lagra flera lokala resurser i en Azure-filresurs förhindrar inte att du skapar de vanliga 15 SMB-resurserna på den lokala Windows-servern. Det innebär bara att du ordnar rotmapparna för dessa 15 resurser som undermappar under en gemensam mapp. Du synkroniserar sedan den här gemensamma mappen med en Azure-filresurs. På så sätt behövs bara en enda Azure-filresurs i molnet för den här gruppen lokala resurser.

#### <a name="volume-sync"></a>Volym synkronisering

Azure File Sync stöder synkronisering av roten för en volym till en Azure-filresurs.
Om du synkroniserar rotmappen kommer alla undermappar och filer att bli kvar i samma Azure-filresurs.

#### <a name="other-best-practices-to-consider"></a>Andra metod tips att tänka på

Förutom den 30 Azure-filresursens Sync-gräns per server är den ledande beräkningen effektiviteten i synkroniseringen.

Om det finns flera resurser på servern som ska synkroniseras med sin egen Azure-filresurs kan synkroniseringen fungera parallellt för alla. Skalnings vektorn är inte storleken på alla filer i ett Sync-omfång. Det är antalet objekt (filer och mappar) som behöver bearbetas.

En enda Azure-filresurs kan innehålla upp till 100 TiB.
Azure File Sync stöder synkronisering av upp till 100 000 000 objekt per Azure-filresurs.

Synkronisering av rot volymen är inte alltid det bästa svaret. Det finns fördelar med att synkronisera flera platser. på så sätt kan du behålla antalet objekt som är lägre per omfångs omfång. Att konfigurera Azure File Sync med ett lägre antal objekt är inte bara viktigt för synkronisering, utan även för att dra nytta av återställningar på moln sidan från säkerhets kopior samt stödja hastigheten för haveri beredskap om du tappar bort servern och etablerar en ny som ansluter till samma Azure-fil e-resurser.

Använd en kombination av koncepten ovan för att avgöra hur många Azure-filresurser du behöver, och vilka delar av dina befintliga StorSimple-data som kommer att få ut i vilken Azure-filresursen.

Skapa en lista som registrerar dina tankar, så att du kan referera till den i nästa steg. Att vara organiserad här är viktigt eftersom det kan vara enkelt att förlora information om din mappnings plan när du konfigurerar många resurser på en gång.
