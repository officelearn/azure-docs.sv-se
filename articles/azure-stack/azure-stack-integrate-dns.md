---
title: Integrering med Azure Stack datacenter - DNS
description: Lär dig hur du integrerar Azure Stack DNS med ditt datacenter DNS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: bf412809f9d10296ad600e64abb6d870dbb88d3e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339686"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integrering med Azure Stack datacenter - DNS
För att kunna komma åt Azure Stack-slutpunkter (**portal**, **adminportal**, **management**, **adminmanagement**osv.)  från utanför Azure Stack måste du integrera Azure Stack-DNS-tjänster med DNS-servrar som är värdar för DNS-zoner som du vill använda i Azure Stack.

## <a name="azure-stack-dns-namespace"></a>Azure Stack-DNS-namnområde
Du måste tillhandahålla viss information relaterad till DNS när du distribuerar Azure Stack.


|Fält  |Beskrivning  |Exempel|
|---------|---------|---------|
|Region|Den geografiska platsen i Azure Stack-distributioner.|`east`|
|Externt domännamn|Namnet på zonen som du vill använda för din Azure Stack-distribution.|`cloud.fabrikam.com`|
|Interna domännamnet|Namnet på den interna zonen som används för infrastrukturtjänster i Azure Stack.  Den är katalogtjänst-integrerade och privat (kan inte nås från utanför Azure Stack-distributioner).|`azurestack.local`|
|DNS-vidarebefordrare|DNS-servrar som används för att vidarebefordra DNS-frågor, DNS-zoner och poster som finns utanför Azure Stack, antingen på företagets intranät eller offentliga internet.|`10.57.175.34`<br>`8.8.8.8`|
|Namnprefix (valfritt)|Namnprefix som du vill att din Azure Stack-infrastruktur instans datorn rollnamn ha.  Om inte anges är standardvärdet `azs`.|`azs`|

Det fullständigt kvalificerade domännamnet (FQDN) Azure Stack-distributioner och slutpunkter är en kombination av Region parametrarna och externa domännamn. Med värden från exemplen i föregående tabell kan är det fullständiga Domännamnet för den här Azure Stack-distributionen följande:

`east.cloud.fabrikam.com`

Exempel på några av slutpunkterna för den här distributionen skulle därför se ut som följande webbadresser:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Om du vill använda det här exemplet DNS-namnområde för en Azure Stack-distribution, krävs följande villkor:

- Zonen `fabrikam.com` har registrerats med en domänregistrator, en intern företagets DNS-server eller båda, beroende på dina krav för namnmatchning.
- Den underordnade domänen `cloud.fabrikam.com` finns under zonen `fabrikam.com`.
- DNS-servrar som är värdar för zonerna `fabrikam.com` och `cloud.fabrikam.com` kan nås från Azure Stack-distributioner.

Om du vill kunna matcha DNS-namn för Azure Stack-slutpunkter och instanser från utanför Azure Stack måste du integrera DNS-servrar som är värdar för DNS-zonen på extern för Azure Stack med DNS-servrar som är värdar för den överordnade zonen som du vill använda.


## <a name="resolution-and-delegation"></a>Matchning och delegering

Det finns två typer av DNS-servrar:

- En auktoritativ DNS-server är värd för DNS-zoner. Den svarar på DNS-frågor om poster i just dessa zoner.
- En rekursiv DNS-server värd inte för DNS-zoner. Den svarar på alla DNS-frågor genom att anropa auktoritativa DNS-servrar för att samla in de data som den behöver.

Azure Stack omfattar både auktoritativa och rekursiva DNS-servrar. De rekursiva servrarna som används för att matcha namn i allt utom den interna privata zonen och externa offentliga DNS-zon för den Azure Stack-distributionen. 

![Azure Stack-DNS-arkitektur](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Externa DNS-namnmatchning från Azure Stack

För DNS-namnmatchning för slutpunkter utanför Azure Stack (till exempel: www.bing.com), måste du ange DNS-servrar som Azure Stack kan använda för att vidarebefordra DNS-begäranden som Azure Stack inte är auktoritär. DNS-servrar som Azure Stack vidarebefordrar begäranden till krävs för distribution i kalkylbladet distribution (i fältet DNS-vidarebefordrare). Ange minst två servrar i det här fältet för feltolerans. Azure Stack-distributionen misslyckas utan dessa värden.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurera villkorlig vidarebefordran av DNS

> [!IMPORTANT]
> Detta gäller endast för en AD FS-distribution.

Konfigurera villkorlig vidarebefordran för att aktivera namnmatchning med din befintliga DNS-infrastruktur.

Om du vill lägga till en villkorlig vidarebefordrare, måste du använda privileged slutpunkten.

För den här proceduren ska du använda en dator i datacenternätverket som kan kommunicera med den privilegierade slutpunkten i Azure Stack.

1. Öppna en upphöjd Windows PowerShell-session (Kör som administratör) och ansluta till IP-adressen för privilegierade slutpunkten. Använd autentiseringsuppgifter för CloudAdmin autentisering.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. När du ansluter till privilegierad slutpunkt, kör du följande PowerShell-kommando. Ersätt exempelvärden som medföljer ditt domännamn och IP-adresserna för DNS-servrar som du vill använda.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Azure Stack DNS-namnmatchning från utanför Azure Stack
Auktoritativa servrar är de som externa DNS-zon informationen och eventuella användarskapade zoner. Integrera med dessa servrar för att aktivera zondelegering eller villkorlig vidarebefordran för Azure Stack DNS-namnmatchning från utanför Azure Stack.

## <a name="get-dns-server-external-endpoint-information"></a>Hämta information om DNS-Server extern slutpunkt

Om du vill integrera din Azure Stack-distribution med DNS-infrastrukturen behöver du följande information:

- DNS-server FQDN
- DNS-serveradresser

FQDN: er för Azure Stack-DNS-servrar har följande format:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Med hjälp av exempelvärden FQDN: er för DNS-är servrar:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Den här informationen skapas också i slutet av alla Azure Stack-distributioner i en fil med namnet `AzureStackStampDeploymentInfo.json`. Den här filen finns i den `C:\CloudDeployment\logs` mappen för den virtuella datorn för distribution. Om du inte vet vilka värden som har använts för distributionen av Azure Stack får du värdena för härifrån.

Om den virtuella datorn för distribution är inte längre tillgänglig eller inte är tillgänglig, kan du hämta värdena genom att ansluta till privilegierad slutpunkt och köra den `Get-AzureStackInfo` PowerShell-cmdlet. Mer information finns i [privilegierad slutpunkt](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Konfigurera villkorlig vidarebefordran i Azure Stack

Det enklaste och säkraste sättet att integrera Azure Stack med DNS-infrastrukturen är att villkorlig vidarebefordran i zonen från den server som är värd för den överordnade zonen. Den här metoden rekommenderas om du har direkt kontroll över DNS-servrar som är värdar för den överordnade zonen för ditt externa DNS-namnområde för Azure Stack.

Om du inte är bekant med hur du gör villkorlig vidarebefordran med DNS, finns i följande TechNet-artikel: [tilldela en villkorlig vidarebefordrare för ett domännamn](https://technet.microsoft.com/library/cc794735), eller i dokumentationen om specifika DNS-lösning.

Villkorlig vidarebefordran kan inte användas i situationer där du har angett din externa Azure Stack DNS-zon ska se ut som en underordnad domän till företagets domännamn. DNS-delegering måste konfigureras.

Exempel:

- Företagets DNS-domännamn: `contoso.com`
- Azure Stack externa DNS-domännamn: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegera den externa DNS-zon till Azure Stack

Du måste konfigurera DNS-delegering för DNS-namn ska lösas från utanför ett Azure Stack-distribution.

Varje registrator har sina egna DNS-hanteringsverktyg för att ändra namnserverposterna för en domän. På registratorns DNS-hanteringssida redigerar du NS-posterna och ersätter NS-posterna för zonen med dem i Azure Stack.

De flesta DNS-registratorer måste du ange minst två DNS-servrar för att slutföra delegeringen.

## <a name="next-steps"></a>Nästa steg

[Integrering med brandväggen](azure-stack-firewall.md)
