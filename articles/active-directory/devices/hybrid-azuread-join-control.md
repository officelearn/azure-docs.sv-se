---
title: Kontrollerad validering av azure AD-hybridanslutning – Azure AD
description: Lär dig hur du gör en kontrollerad validering av hybrid Azure AD-koppling innan du aktiverar den i hela organisationen på en gång
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049985"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Kontrollerad verifiering av Azure AD-anslutningshybrid

När alla förutsättningar finns på plats registreras Windows-enheter automatiskt som enheter i din Azure AD-klientorganisation. Tillståndet för dessa enhetsidentiteter i Azure AD kallas hybrid Azure AD-koppling. Mer information om begreppen som beskrivs i den här artikeln finns i artiklarna [Introduktion till enhetshantering i Azure Active Directory](overview.md) och planera [implementeringen av din hybrid-Azure Active Directory-anslutning](hybrid-azuread-join-plan.md).

Organisationer kanske vill göra en kontrollerad validering av hybrid Azure AD-koppling innan de aktiverar den i hela organisationen på en gång. Den här artikeln förklarar hur du utför en kontrollerad validering av hybrid Azure AD-koppling.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Kontrollerad validering av azure AD-hybridkoppling på aktuella Windows-enheter

För enheter som kör Windows-operativsystem för stationära datorer är den version som stöds Windows 10 Anniversary Update (version 1607) eller senare. Som bästa praxis bör du uppgradera till den senaste versionen av Windows 10.

Om du vill göra en kontrollerad validering av hybrid-Azure AD-koppling på aktuella Windows-enheter måste du:

1. Avmarkera tjänstens anslutningspunkt (SCP) från Active Directory (AD) om den finns
1. Konfigurera registerinställning på klientsidan för SCP på domänens anslutna datorer med hjälp av ett grupprincipobjekt (GPO)
1. Om du använder AD FS måste du också konfigurera registerinställningen på klientsidan för SCP på AD FS-servern med hjälp av ett GPO  
1. Du kan också behöva [anpassa synkroniseringsalternativen](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) i Azure AD Connect för att aktivera enhetssynkronisering. 


### <a name="clear-the-scp-from-ad"></a>Rensa SCP från AD

Använd ADSI-redigeraren (Active Directory Services Interfaces Editor) för att ändra SCP-objekten i AD.

1. Starta **programmet ADSI Edit** från och administrativ arbetsstation eller domänkontrollant som företagsadministratör.
1. Anslut till **konfigurationsnamnkontexten** för din domän.
1. Bläddra till **CN=Configuration,DC=contoso,DC=com** > **CN=Services** > **CN=Device Registration Configuration**
1. Högerklicka på bladobjektet **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** och välj **Egenskaper**
   1. Välj nyckelord i fönstret **Attributredigerare** och klicka på **Redigera** **keywords**
   1. Välj värden för **azureADId** och **azureADName** (ett i taget) och klicka på **Ta bort**
1. Stäng **ADSI-redigering**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurera registerinställning på klientsidan för SCP

Använd följande exempel för att skapa ett grupprincipobjekt (GPO) för att distribuera en registerinställning som konfigurerar en SCP-post i registret på dina enheter.

1. Öppna en grupprinciphanteringskonsol och skapa ett nytt grupprincipobjekt på domänen.
   1. Ge ditt nyskapade GPO ett namn (till exempel ClientSideSCP).
1. Redigera den globala användarobjektet och hitta följande sökväg: **Datorkonfigurationsinställningar** > **Windows-inställningsregister** > **Registry** **Preferences** > 
1. Högerklicka på registret och välj **Nytt** > **registerobjekt**
   1. Konfigurera följande på fliken **Allmänt**
      1. Åtgärd: **Uppdatera**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Nyckelväg: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Värdenamn: **TenantId**
      1. Värdetyp: **REG_SZ**
      1. Värdedata: GUID- eller **katalog-ID för** din Azure AD-instans (Det här värdet finns i **Azure Portal** > **Azure Active Directory** > **Properties** > **Directory ID**)
   1. Klicka på **OK**
1. Högerklicka på registret och välj **Nytt** > **registerobjekt**
   1. Konfigurera följande på fliken **Allmänt**
      1. Åtgärd: **Uppdatera**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Nyckelväg: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Värdenamn: **Klientnamn**
      1. Värdetyp: **REG_SZ**
      1. Värdedata: Ditt verifierade **domännamn** om du använder federerade miljöer som AD FS. Ditt verifierade **domännamn** eller ditt onmicrosoft.com domännamn, `contoso.onmicrosoft.com` till exempel om du använder hanterad miljö
   1. Klicka på **OK**
1. Stäng redaktören för det nyskapade GPO
1. Länka det nyskapade GPO till önskad organisationsenhet som innehåller domänanslutna datorer som tillhör din kontrollerade distributionspopulation

### <a name="configure-ad-fs-settings"></a>Konfigurera AD FS-inställningar

Om du använder AD FS måste du först konfigurera SCP på klientsidan med hjälp av instruktionerna ovan genom att länka GPO till DINA AD FS-servrar. SCP-objektet definierar behörighetskällan för enhetsobjekt. Det kan vara lokalt eller Azure AD. När SCP på klientsidan har konfigurerats för AD FS upprättas källan för enhetsobjekt som Azure AD.

> [!NOTE]
> Om du inte kunde konfigurera SCP på klientsidan på AD FS-servrarna betraktas källan för enhetsidentiteter som lokal. ADFS börjar sedan ta bort enhetsobjekt från den lokala katalogen efter den angivna perioden som definieras i ADFS-enhetsregistreringens attribut "MaximumInactiveDays". ADFS Enhetsregistreringsobjekt finns med hjälp av [cmdleten Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Kontrollerad validering av azure AD-hybridkoppling på Enheter på Windows på lägre nivå

Om du vill registrera Windows enheter på lägre nivå måste organisationer installera [Microsoft Workplace Join för datorer som inte är tillgängliga i Microsoft 10](https://www.microsoft.com/download/details.aspx?id=53554) på Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett programdistributionssystem som [Microsoft Endpoint Configuration Manager](/configmgr/). Paketet stöder standardalternativen för tyst installation med den tysta parametern. Den aktuella grenen av Configuration Manager erbjuder fördelar jämfört med tidigare versioner, till exempel möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet i systemet som körs i användarkontexten. Uppgiften utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst enheten med Azure AD med användarautentiseringsuppgifterna efter autentisering med Azure AD.

Om du vill styra enhetsregistreringen bör du distribuera Windows Installer-paketet till den valda gruppen av Windows-enheter på lägre nivå.

> [!NOTE]
> Om en SCP inte har konfigurerats i AD bör du följa samma metod som beskrivs för att [konfigurera registerinställning på klientsidan för SCP](#configure-client-side-registry-setting-for-scp)) på domänens anslutna datorer med hjälp av ett grupprincipobjekt (GPO).


När du har verifierat att allt fungerar som förväntat kan du automatiskt registrera resten av windows nuvarande och ned-nivå-enheter med Azure AD genom [att konfigurera SCP med Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Nästa steg

[Planera implementeringen av Azure Active Directory-hybridanslutning](hybrid-azuread-join-plan.md)
