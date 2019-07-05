---
title: Kontrollerad validering av hybrid Azure AD join - Azure AD
description: Lär dig hur du gör en kontrollerad validering av hybrid Azure AD-anslutning innan du aktiverar i hela organisationen på samma gång
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
ms.openlocfilehash: d59104bf9c7675fdac2c245fff89ab1483b96b67
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481727"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Kontrollerad verifiering av Azure AD-anslutningshybrid

När alla krav är uppfyllda, kommer Windows-enheter automatiskt att registreras som enheter i din Azure AD-klient. Tillståndet för de här enhetsidentiteter i Azure AD kallas hybrid Azure AD-anslutning. Mer information om begrepp i den här artikeln finns i artiklarna [introduktionen till enhetshantering i Azure Active Directory](overview.md) och [planera implementeringen hybrid Azure Active Directory join ](hybrid-azuread-join-plan.md).

Organisationer vilja göra en kontrollerad validering av hybrid Azure AD-anslutning innan du aktiverar i hela organisationen på samma gång. Den här artikeln förklarar hur du utför en kontrollerad validering av hybrid Azure AD-anslutning.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Kontrollerad validering av hybrid Azure AD join i befintliga Windows-enheter

För enheter som kör Windows operativsystem, versionen som stöds är Windows 10 Anniversary Update (version 1607) eller senare. Ett bra tips är att uppgradera till den senaste versionen av Windows 10.

Om du vill göra en kontrollerad validering av hybrid Azure AD join i befintliga Windows-enheter, måste du:

1. Ta bort posten tjänstanslutningspunkt (SCP) från Active Directory (AD) om den finns
1. Konfigurera registerinställningar på klientsidan för SCP på domänanslutna datorer med hjälp av ett grupprincipobjekt (GPO)
1. Om du använder AD FS måste du också konfigurera registerinställningen för klientsidan för SCP på AD FS-servern med hjälp av ett grupprincipobjekt  



### <a name="clear-the-scp-from-ad"></a>Ta bort SCP: N från AD

Använd den Active Directory Services Interfaces Editor (ADSI-redigering) för att ändra SCP-objekt i AD.

1. Starta den **ADSI-redigering** skrivbordsprogram från och administrativ arbetsstation eller en domänkontrollant som en företagsadministratör.
1. Ansluta till den **Configuration namngivningskontext** på din domän.
1. Bläddra till **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = enhet registrering Configuration**
1. Högerklicka på objektet löv under **CN = enhet registrering Configuration** och välj **egenskaper**
   1. Välj **nyckelord** från den **Attributredigeraren** och klicka på **redigera**
   1. Välj värdena för **azureADId** och **azureADName** (en i taget) och klicka på **ta bort**
1. Stäng **ADSI-redigering**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurera registerinställningar på klientsidan för SCP

Använd följande exempel för att skapa ett grupprincipobjekt (GPO) för att distribuera en registerinställning som konfigurerar en SCP-post i registret på dina enheter.

1. Öppna konsolen Grupprinciphantering och skapa ett nytt grupprincipobjekt i domänen.
   1. Ange ett namn (till exempel ClientSideSCP) för ditt nyligen skapade GPO.
1. Redigera Grupprincipobjektet och leta upp följande sökväg: **Datorkonfiguration** > **inställningar** > **Windows-inställningar** > **registret**
1. Högerklicka på registernyckeln och välj **New** > **registerobjekt**
   1. På den **Allmänt** konfigurerar följande
      1. Åtgärd: **Uppdatering**
      1. Registreringsdatafil: **HKEY_LOCAL_MACHINE**
      1. Nyckelsökväg: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Värdenamn: **TenantId**
      1. Värdetyp: **REG_SZ**
      1. Värdedata: GUID eller **katalog-ID** för din Azure AD-instans (det här värdet finns i den **Azure-portalen** > **Azure Active Directory**  >   **Egenskaper för** > **katalog-ID**)
   1. Klicka på **OK**
1. Högerklicka på registernyckeln och välj **New** > **registerobjekt**
   1. På den **Allmänt** konfigurerar följande
      1. Åtgärd: **Uppdatering**
      1. Registreringsdatafil: **HKEY_LOCAL_MACHINE**
      1. Nyckelsökväg: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Värdenamn: **tenantName**
      1. Värdetyp: **REG_SZ**
      1. Värdedata: Din verifierade **domännamn** i Azure AD (till exempel `contoso.onmicrosoft.com` eller några andra verifierat domännamn i katalogen)
   1. Klicka på **OK**
1. Stäng Redigeraren för den nyligen skapade GPO
1. Länka det nya Grupprincipobjektet till Organisationsenheten önskade som innehåller domänanslutna datorer som tillhör din kontrollerad distribution population

### <a name="configure-ad-fs-settings"></a>Konfigurera inställningar för AD FS

Om du använder AD FS måste du först konfigurera SCP för klientsidan med hjälp av anvisningarna som nämns ovan, men länkning av Grupprincipobjektet till AD FS-servrarna. Den här konfigurationen krävs för AD FS för att upprätta källan för enhetsidentiteter som Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Kontrollerad validering av hybrid Azure AD join i Windows äldre enheter

Om du vill registrera Windows äldre enheter, organisationer måste installera [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554) finns på Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Paketet stöder alternativen standard tyst installation med parametern tyst. Den aktuella grenen av Configuration Manager ger fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarkontexten. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften kopplar tyst enhet med Azure AD med autentiseringsuppgifterna för användaren när de har autentiserat med Azure AD.

För att styra enhetsregistreringen, bör du distribuera Windows Installer-paketet på din valda grupp av Windows äldre enheter.

> [!NOTE]
> Om en SCP inte har konfigurerats i AD så du bör följa samma metod som beskrivs till [konfigurera registerinställningar på klientsidan för SCP](#configure-client-side-registry-setting-for-scp)) på domänanslutna datorer med hjälp av ett grupprincipobjekt (GPO).


När du har kontrollerat att allt fungerar som förväntat kan du automatiskt registrera resten av dina aktuella och äldre enheter i Windows med Azure AD genom [konfigurera SCP med Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Nästa steg

[Planera implementeringen av Azure Active Directory-hybridanslutning](hybrid-azuread-join-plan.md)
