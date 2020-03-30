---
title: Mappa en mappstruktur till en Azure File Sync-topologi
description: Mappa en befintlig fil- och mappstruktur till Azure-filresurser för användning med Azure File Sync. Ett vanligt textblock som delas mellan migreringsdokument.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124735"
---
I det här steget utvärderar du hur många Azure-filresurser du behöver. En enda Windows Server (eller kluster) kan synkronisera upp till 30 Azure-filresurser.

Du kan ha fler mappar på dina volymer som du för närvarande delar ut lokalt som SMB-resurser till dina användare och appar. Det enklaste skulle vara att föreställa sig en lokal resurs för att mappa 1:1 till en Azure-filresurs. Om du har ett tillräckligt litet nummer under 30 för en enda Windows Server rekommenderas en 1:1-mappning.

Om du har fler resurser än 30 är det ofta onödigt att mappa en lokal resurs 1:1 till en Azure-filresurs.
Tänk på följande alternativ:

#### <a name="share-grouping"></a>Dela gruppering

Om din HR-avdelning till exempel har totalt 15 resurser kan du överväga att lagra alla HR-data i en enda Azure-filresurs. Att lagra flera lokala resurser i en Azure-filresurs hindrar dig inte från att skapa de vanliga 15 SMB-resurserna på din lokala Windows Server. Det betyder bara att du organiserar rotmapparna för dessa 15 resurser som undermappar under en gemensam mapp. Du synkroniserar sedan den här gemensamma mappen med en Azure-filresurs. På så sätt behövs bara en enda Azure-filresurs i molnet för den här gruppen av lokala resurser.

#### <a name="volume-sync"></a>Volymsynkronisering

Azure File Sync stöder synkronisering av roten för en volym till en Azure-filresurs.
Om du synkroniserar rotmappen hamnar alla undermappar och filer i samma Azure-filresurs.

Synching roten av volymen kommer inte alltid att vara det bästa svaret. Det finns fördelar med att synkronisera flera platser, vilket gör det hjälper till att hålla antalet objekt lägre per synkroniseringsomfång. Att konfigurera Azure File Sync med ett lägre antal objekt är inte bara fördelaktigt för filsynkronisering. Ett lägre antal objekt gynnar också andra scenarier som:

* återställning på molnsidan från en ögonblicksbild av Azure-filresurs som tagits som en säkerhetskopia
* haveriberedskap av en lokal server kan påskynda avsevärt
* ändringar som görs direkt i en Azure-filresurs (utanför synkroniseringen) kan identifieras och synkroniseras snabbare

#### <a name="a-structured-approach-to-a-deployment-map"></a>Ett strukturerat tillvägagångssätt för en distributionskarta

Innan du distribuerar molnlagring i ett efterföljande steg är det viktigt att skapa en karta mellan lokala mappar och Azure-filresurser. Den här mappningen informerar sedan hur många och vilka Azure File Sync-resurser för synkroniseringsgrupp som du ska etablera. En synkroniseringsgrupp binder Azure-filresursen och mappen på servern tillsammans och upprättar en synkroniseringsanslutning.

Om du vill ta beslutet om hur många Azure-filresurser du behöver läser du följande begränsningar och metodtips. Om du gör det kan du optimera kartan:

* En server med Azure File Sync-agenten installerad kan synkroniseras med upp till 30 Azure-filresurser.
* En Azure-filresurs distribueras i ett lagringskonto. Det gör lagringskontot till ett skalningsmål för prestandanummer som IOPS och dataflöde. Två standard -(inte premium) Azure-filresurser kan teoretiskt mätta den maximala prestanda som ett lagringskonto kan leverera. Om du planerar att bara bifoga Azure File Sync till dessa filresurser, då gruppera flera Azure-filresurser i samma lagringskonto inte skapa ett problem. Granska prestandamålen för Azure-filresurs för djupare insikt i relevanta mått att tänka på. Om du planerar att lyfta en app till Azure som använder Azure-filresursen internt kan du behöva mer prestanda från din Azure-filresurs. Om detta är en möjlighet, även i framtiden, då mappa en Azure-filresurs till sitt eget lagringskonto är bäst.
* Det finns en gräns på 250 lagringskonton per prenumeration i en enda Azure-region.

> [!TIP]
> Med den här informationen i åtanke blir det ofta nödvändigt att gruppera flera mappar på den högsta nivån på dina volymer i en gemensam, ny rotkatalog. Du synkroniserar sedan den nya rotkatalogen och alla mappar som du grupperade i den till en enda Azure-filresurs.                                                    

Med den här tekniken kan du hålla dig inom synkroniseringsgränsen för 30 Azure-filresurs per server.
Den här grupperingen under en gemensam rot påverkar inte åtkomsten till dina data. Dina ACL:er förblir som de är, du behöver bara justera alla resurssökvägar (som SMB- eller NFS-resurser) som du kan ha på de servermappar som du nu har ändrat till en gemensam rot. Inget annat förändras.

En annan viktig aspekt av Azure File Sync och en balanserad prestanda och erfarenhet är en förståelse av skalningsfaktorerna för Azure File Sync-prestanda. Självklart, när filer synkroniseras över Internet, större filer tar mer tid och bandbredd för att synkronisera.

> [!IMPORTANT]
> Den viktigaste skalvektorn för Azure File Sync är antalet objekt (filer och mappar) som måste synkroniseras.

Azure File Sync stöder synkronisering av upp till 100 000 objekt till en enda Azure-filresurs. Den här gränsen kan överskridas och visar bara vad Azure File Sync-teamet testar regelbundet.

Det är en bra idé att hålla antalet objekt per synkroniseringsomfång lågt. Den aspekten är en viktig faktor som ska beaktas vid mappningen av mappar till Azure-filresurser.

Även om en uppsättning mappar i din situation logiskt kan synkroniseras med samma Azure-filresurs (med den nya, gemensamma rotmappsmetoden ovanifrån) kan det fortfarande vara bättre att gruppera mappar så att de synkroniseras till två i stället för en Azure-filresurs. Den här metoden kan användas för att hålla antalet filer och mappar per filresurs balanserat över servern.

#### <a name="create-a-mapping-table"></a>Skapa en mappningstabell

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Använd en kombination av de tidigare begreppen för att avgöra hur många Azure-filresurser du behöver och vilka delar av dina befintliga data som hamnar i vilken Azure-filresurs.
        
        Skapa en tabell som registrerar dina tankar, så att du kan referera till den i nästa steg. Att hålla ordning är viktigt eftersom det kan vara lätt att förlora information om din mappningsplan när du etablerar många Azure-resurser samtidigt. Om du vill hjälpa dig att skapa en fullständig mappning kan du hämta en Microsoft Excel-fil som en mall.

[//]: # (HTML visas som det enda sättet att åstadkomma att lägga till en kapslad tabell med två kolumner med arbetsbildtolkning och text/hyperlänk på samma rad.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Hämta en namnområdesmappningsmall.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
