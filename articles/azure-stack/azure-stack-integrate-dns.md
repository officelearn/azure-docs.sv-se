---
title: Integration av Azure Stack datacenter - DNS
description: "Lär dig att integrera Azure Stack DNS med ditt datacenter DNS"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 5bdac2f3e6082f9449800fe2d4b303e2d59ade46
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integration av Azure Stack datacenter - DNS
För att kunna komma åt Azure Stack slutpunkter (`portal`, `adminportal`, `management`, `adminmanagement`osv.)  Du måste integrera Azure Stack DNS-tjänster med DNS-servrar som är värdar för DNS-zoner som du vill använda i Azure-stacken från utanför Azure-stacken.

## <a name="azure-stack-dns-namespace"></a>Azure-stacken DNS-namnområde
Du måste tillhandahålla vissa viktig information som rör DNS när du distribuerar Azure stacken.


|Fält  |Beskrivning  |Exempel|
|---------|---------|---------|
|Region|Den geografiska platsen för din Azure Stack-distribution.|`east`|
|Externa domännamn|Namnet på zonen som du vill använda för din Azure Stack-distribution.|`cloud.fabrikam.com`|
|Interna domännamnet|Namnet på den interna zonen som används för infrastrukturtjänster i Azure-stacken.  Det är integrerat i katalogtjänsten och privata (kan inte nås från utanför Azure Stack-distribution).|`azurestack.local`|
|DNS-vidarebefordrare|DNS-servrar som används för att vidarebefordra DNS-frågor, DNS-zoner och poster som finns utanför Azure-stacken, antingen på företagets intranät eller offentliga internet.|`10.57.175.34`<br>`8.8.8.8`|
|Namnprefix (valfritt)|Namnprefix som du vill att din Azure-stacken infrastruktur instans datorn rollnamn har.  Om inte anges, används som standard `azs`.|`azs`|

Fullständigt kvalificerade domännamn (FQDN) Azure Stack-distribution och slutpunkter är en kombination av Region parametrarna och externa domännamn. Med hjälp av värdena från exemplen i föregående tabell är FQDN för den här distributionen av Azure-stacken följande:

`east.cloud.fabrikam.com`

Som ser exempel på några av slutpunkter för den här distributionen ut som följande webbadresser:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Om du vill använda det här exemplet DNS-namnområde för distribution av en Azure-stacken, krävs följande:

- Zonen `fabrikam.com` har registrerats med en domänregistrator, en företagets interna DNS-server eller både och, beroende på dina krav för namnmatchning.
- Den underordnade domänen `cloud.fabrikam.com` finns i zonen `fabrikam.com`.
- DNS-servrar som är värdar för zonerna `fabrikam.com` och `cloud.fabrikam.com` kan nås från Azure Stack-distribution.

Om du vill kunna matcha DNS-namn för Azure-stacken slutpunkter och instanser från utanför Azure-stacken måste du integrera DNS-servrar som är värdar för externa DNS-zonen för Azure-stacken med DNS-servrar som är värdar för den överordnade zonen som du vill använda.


## <a name="resolution-and-delegation"></a>Matchning och delegering

Det finns två typer av DNS-servrar:

- En auktoritativ DNS-server är värd för DNS-zoner. Den svarar på DNS-frågor om poster i just dessa zoner.
- En rekursiv DNS-server inte är värd för DNS-zoner. Den svarar på alla DNS-frågor genom att anropa auktoritativa DNS-servrar för att samla in de data som den behöver.

Azure-stacken innehåller både auktoritära och rekursiva DNS-servrar. Rekursiva servrar för att matcha namnen på allt utom den interna privata zonen och externa offentliga DNS-zon för den Azure Stack-distributionen. 

![Azure DNS-Stack-arkitektur](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Externa DNS-namnmatchning från Azure-stacken

För DNS-namnmatchning för slutpunkter utanför Azure-stacken (till exempel: www.bing.com), måste du ange DNS-servrar som Azure Stack kan använda för att vidarebefordra som Azure-stacken inte är auktoritära DNS-förfrågningar. DNS-servrar som Azure Stack vidarebefordrar begäranden till krävs distribution, i kalkylbladet distribution (i fältet DNS-vidarebefordrare). Ange minst två servrar i det här fältet för feltolerans. Utan dessa värden misslyckas Azure Stack distributionen.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurera villkorlig vidarebefordran av DNS

> [!IMPORTANT]
> Detta gäller endast för en AD FS-distribution.

Konfigurera villkorlig vidarebefordran för att aktivera namnmatchning i den befintliga DNS-infrastrukturen.

Om du vill lägga till en villkorlig vidarebefordrare, måste du använda Privilegierade slutpunkten.

Använda en dator i ditt datacenternätverk som kan kommunicera med den privilegierade slutpunkten i Azure-stacken för den här proceduren.

1. Öppna en upphöjd Windows PowerShell-session (Kör som administratör) och ansluta till IP-adressen för privilegierade slutpunkten. Använd autentiseringsuppgifter för CloudAdmin autentisering.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. När du ansluter till Privilegierade slutpunkten kör du följande PowerShell-kommando. Ersätt exempelvärden med ditt domännamn och IP-adresser för DNS-servrar som du vill använda.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Azure-stacken DNS-namnmatchning från utanför Azure-stacken
Auktoritativa servrar är de som externa DNS-zonen informationen och användardefinierade zoner. Integrera med dessa servrar för att aktivera zondelegering eller villkorlig vidarebefordran för Azure-stacken DNS-namnmatchning från utanför Azure-stacken.

## <a name="get-dns-server-external-endpoint-information"></a>Hämta information om DNS-Server extern slutpunkt

Om du vill integrera din Azure Stack-distribution med DNS-infrastrukturen behöver du följande information:

- DNS-server FQDN
- IP-adresser för DNS-servrar

FQDN för Azure-stacken DNS-servrar har följande format:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Med hjälp av exempelvärden FQDN för DNS-är servrar:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Den här informationen skapas också i slutet av alla Azure-stacken distributioner i en fil med namnet `AzureStackStampDeploymentInfo.json`. Den här filen finns i den `C:\CloudDeployment\logs` mappen för den virtuella datorn för distribution. Om du inte vet vilka värden som har använts för din Azure Stack-distribution kan du värdena från den här.

Om den virtuella datorn för distribution är inte längre tillgänglig eller inte är tillgänglig, kan du hämta värdena genom att ansluta till Privilegierade slutpunkten och kör den `Get-AzureStackInfo` PowerShell-cmdlet. Mer information om Privilegierade slutpunkten finns (Infoga länk för att den här artikeln).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Konfigurera villkorlig vidarebefordran till Azure-stacken

Det enklaste och säkraste sättet att integrera Azure stacken med DNS-infrastrukturen är att villkorlig vidarebefordran i zonen från servern som är värd för den överordnade zonen. Den här metoden rekommenderas om du har direkt kontroll över DNS-servrar som är värdar för den överordnade zonen för din Azure-stacken externt DNS-namnområde.

Om du inte känner till hur du gör villkorlig vidarebefordran med DNS finns i följande TechNet-artikel: [tilldela en villkorlig vidarebefordrare för ett domännamn](https://technet.microsoft.com/library/cc794735), eller i dokumentationen om specifika DNS-lösningen.

Villkorlig vidarebefordran kan inte användas i scenarier där du har angett din externa Azure Stack DNS-zonen ska se ut som en underordnad domän till företagets domännamn. DNS-delegering måste konfigureras.

Exempel:

- Corporate DNS Domain Name: `contoso.com`
- Azure-stacken externa DNS-domännamn: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegera externa DNS-zonen till Azure-stacken

Du måste ställa in DNS-delegering för DNS-namn ska kunna lösas från utanför en Azure-Stack-distribution.

Varje registrator har sina egna DNS-hanteringsverktyg för att ändra namnserverposterna för en domän. I den registrator DNS management-sidan Redigera NS-poster och Ersätt NS-poster för zonen med dem i Azure-stacken.

De flesta DNS-registratorer måste du ange minst två DNS-servrar för att slutföra delegeringen.

## <a name="next-steps"></a>Nästa steg

[Integrering med brandväggen](azure-stack-firewall.md)
