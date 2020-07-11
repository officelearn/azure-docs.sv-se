---
title: 'Lista över säkra URL: er för Windows Virtual Desktop – Azure'
description: 'En lista med URL: er som du bör avblockera för att se till att distributionen av Windows virtuella datorer fungerar som avsett.'
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9b1bdfc326ff217e68785d823b4af046af3241b7
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225300"
---
# <a name="safe-url-list"></a>Lista över säkra URL: er

Du måste avblockera vissa URL: er så att distributionen av Windows virtuella datorer fungerar som den ska. I den här artikeln visas dessa URL: er så att du vet vilka som är säkra.

## <a name="virtual-machines"></a>Virtuella datorer

De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste ha åtkomst till följande URL: er:

|Adress|Utgående TCP-port|Syfte|Service tag|
|---|---|---|---|
|*. wvd.microsoft.com|443|Tjänst trafik|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Uppdateringar av agent-och SXS-stack|AzureCloud|
|*.core.windows.net|443|Agent trafik|AzureCloud|
|*.servicebus.windows.net|443|Agent trafik|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Agent trafik|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-aktivering|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal support|AzureCloud|

>[!IMPORTANT]
>Windows Virtual Desktop stöder nu FQDN-taggen. Mer information finns i [använda Azure Firewall för att skydda fönster distributioner av virtuella skriv bord](../firewall/protect-windows-virtual-desktop.md).
>
>Vi rekommenderar att du använder FQDN-taggar eller tjänst Taggar i stället för URL: er för att förhindra tjänst problem. URL: er och taggar i listan motsvarar endast Windows virtuella Skriv bords webbplatser och resurser. De omfattar inte URL: er för andra tjänster som Azure Active Directory.

I följande tabell visas valfria URL: er som dina virtuella Azure-datorer kan ha åtkomst till:

|Adress|Utgående TCP-port|Syfte|Service tag|
|---|---|---|---|
|*.microsoftonline.com|443|Autentisering till Microsoft Online Services|Inga|
|*. events.data.microsoft.com|443|Telemetri-tjänst|Inga|
|www.msftconnecttest.com|443|Identifierar om operativ systemet är anslutet till Internet|Inga|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Inga|
|login.windows.net|443|Logga in på Microsoft Online Services, Microsoft 365|Inga|
|*. sfx.ms|443|Uppdateringar för OneDrive-klientprogramvara|Inga|
|*. digicert.com|443|Återkallnings kontroll av certifikat|Inga|

>[!NOTE]
>Det finns för närvarande ingen lista över IP-adressintervall som kan avblockeras för att tillåta nätverks trafik i det virtuella Windows-skrivbordet. Vi har bara stöd för att avblockera vissa URL: er just nu.
>
>En lista över säkra Office-relaterade URL: er, inklusive nödvändiga Azure Active Directory-relaterade URL: er, finns i [Office 365-URL: er och IP-adressintervall](/office365/enterprise/urls-and-ip-address-ranges).
>
>Du måste använda jokertecknet (*) för URL: er som involverar tjänst trafiken. Om du inte vill använda * för agent-relaterad trafik så här hittar du URL: erna utan jokertecken:
>
>1. Registrera dina virtuella datorer på Windows-poolen för virtuella skriv bord.
>2. Öppna **logg boken**och gå sedan till **Windows loggar**  >  **Application**  >  **WVD-agent** och leta efter händelse-ID 3702.
>3. Vitlista de URL: er som du hittar under händelse-ID 3702. URL: erna under händelse-ID 3702 är landsspecifika. Du måste upprepa den avblockerande processen med relevanta URL: er för varje region som du vill distribuera dina virtuella datorer i.

## <a name="remote-desktop-clients"></a>Fjärrskrivbordsklienter

Alla fjärr skrivbords klienter som du använder måste ha åtkomst till följande URL: er:

|Adress|Utgående TCP-port|Syfte|Klient (er)|
|---|---|---|---|
|*. wvd.microsoft.com|443|Tjänst trafik|Alla|
|*.servicebus.windows.net|443|Felsöka data|Alla|
|go.microsoft.com|443|Microsoft-FWLinks|Alla|
|aka.ms|443|Microsoft URL-kortare|Alla|
|docs.microsoft.com|443|Dokumentation|Alla|
|privacy.microsoft.com|443|Sekretesspolicy|Alla|
|query.prod.cms.rt.microsoft.com|443|Klient uppdateringar|Windows-skrivbordet|

>[!IMPORTANT]
>Att öppna dessa URL: er är viktigt för en tillförlitlig klient upplevelse. Det finns inte stöd för att blockera åtkomst till dessa URL: er och det påverkar service funktionerna. Dessa URL: er motsvarar bara klientens platser och resurser och inkluderar inte URL: er för andra tjänster som Azure Active Directory.