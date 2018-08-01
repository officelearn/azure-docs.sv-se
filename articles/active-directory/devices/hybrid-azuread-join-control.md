---
title: Hur du konfigurerar hybrid Azure Active Directory-anslutna enheter | Microsoft Docs
description: Lär dig mer om att konfigurera hybrid Azure Active Directory-anslutna enheter.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 2332923946e414325b9723a59cf493d9d1060cc6
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369179"
---
# <a name="how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Så här kontrollerar du hybrid Azure AD-anslutning av dina enheter

Hybrid Azure AD-anslutning är en process för att automatiskt registrera dina lokala domänanslutna enheter med Azure AD. Det finns fall där du inte vill att alla dina enheter att registrera automatiskt. Det här är för exempel SANT under den inledande distributionen för att kontrollera att allt fungerar som förväntat.

Den här artikeln ger vägledning om hur du kan styra hybrid Azure AD join för dina enheter. 


## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med:

-  [Introduktion till hantering av enheter i Azure Active Directory](../device-management-introduction.md)
 
-  [Hur du planerar din hybrid Azure Active Directory join-implementering](hybrid-azuread-join-plan.md)

-  Konfigurera hybrid Azure Active Directory-anslutning för [hanterade domäner](hybrid-azuread-join-managed-domains.md) eller [federerade domäner](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Styra aktuella Windows-enheter

För enheter som kör Windows operativsystem, versionen som stöds är Windows 10 Anniversary Update (version 1607) eller senare. Ett bra tips är att uppgradera till den senaste versionen av Windows 10.

Alla windows befintliga enheter automatiskt registrera med Azure AD vid starten av enheten eller användaren logga in. Du kan styra detta med ett grupprincipobjekt (GPO) eller System Center Configuration Manager.

För att styra aktuella Windows-enheter, måste du: 

1.  **Till alla enheter**: inaktivera automatisk enhetsregistrering.
2.  **Till valda enheter**: aktivera automatisk enhetsregistrering.

Om du har kontrollerat att allt fungerar som förväntat, är du redo att aktivera automatisk enhetsregistrering för alla enheter igen.



## <a name="group-policy-object"></a>Grupprincipobjekt 

Du kan styra beteendet enheten registreringen av dina enheter genom att distribuera följande Grupprincipobjektet: **registrera domänanslutna datorer som enheter**

**Ange Grupprincipobjektet**:

1.  Öppna **Serverhanteraren**, och gå till **verktyg \> Group Policy Management**.

2.  Gå till domännoden som motsvarar den domän där du vill aktivera/inaktivera automatisk registrering.

3.  Högerklicka på **grupprincipobjekt**, och välj sedan **New**.

4.  Skriv ett namn (till exempel *Hybrid Azure AD-anslutning*) för gruppolicy-objekt. 

5.  Klicka på **OK**.

6.  Högerklicka på ditt nya GPO och välj sedan **redigera**.

7.  Gå till **Datorkonfiguration \> principer \> Administrationsmallar \> Windows-komponenter \> Enhetsregistrering**. 

8.  Högerklicka på **registrera domänanslutna datorer som enheter**, och välj sedan **redigera**.

    > [!NOTE] 
    > Den här Grupprincipmall har ändrats från tidigare versioner av konsolen Grupprinciphantering. Om du använder en tidigare version av konsolen går du till **Datorkonfiguration \> principer \> Administrationsmallar \> Windows-komponenter \> Arbetsplatsanslutning \> Automatiskt workplace join-klientdatorer**. 

9.  Välj något av följande inställningar och klicka sedan på **tillämpa**:

    - **Inaktiverad** – om du vill förhindra automatisk enhetsregistrering
    - **Aktiverad** – om du vill aktivera automatisk enhetsregistrering

10. Klicka på **OK**.

Du måste länka Grupprincipobjektet till en valfri plats. Till exempel ange den här principen för alla domänanslutna aktuella enheter i din organisation och länka Grupprincipobjektet till domänen. Gör en kontrollerad distribution genom att ställa in principen till domänanslutna Windows befintliga enheter som hör till en organisationsenhet eller en säkerhetsgrupp.

### <a name="configuration-manager-controlled-deployment"></a>Configuration Manager kontrollerad distribution 

Du kan styra beteendet enheten registreringen av din befintliga enheter genom att konfigurera följande klientinställning: ** automatiskt registrera nya Windows 10 domänanslutna enheter med azure Active Directory **


**Att ställa in klientinställningen**:

1.  Öppna **Configuration Manager**, och gå till **molntjänster**.

2.  Under **Enhetsinställningar**, väljer du något av följande inställningar för **automatiskt registrera nya Windows 10 domänanslutna enheter med azure Active Directory**:

    - **Inte** – om du vill förhindra automatisk enhetsregistrering
    - **Ja** – om du vill aktivera automatisk enhetsregistrering


3.  Klicka på **OK**.
    

Du måste länka den här klientinställningen till en valfri plats. Till exempel för att konfigurera den här klientinställningen för alla aktuella Windows-enheter i din organisation, länka klientinställningen till domänen. Du kan konfigurera för klienten att domänanslutna Windows befintliga enheter som hör till en organisationsenhet eller en säkerhetsgrupp om du vill göra en kontrollerad distribution.

## <a name="control-windows-down-level-devices"></a>Styra Windows äldre enheter

Om du vill registrera Windows äldre enheter, som du behöver hämta och installera Windows Installer-paketet (.msi) från Download Center på den [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/en-us/download/details.aspx?id=53554) sidan.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som System Center Configuration Manager. Paketet stöder alternativ för standard tyst installation med parametern tyst. [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) Current Branch ger ytterligare fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarens kontext. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften kopplar tyst enhet med Azure AD med autentiseringsuppgifterna för användaren när de har autentiserat med Azure AD.


För att styra enhetsregistreringen, bör du distribuera Windows Installer-paketet enbart för en grupp av Windows äldre enheter. Om du har kontrollerat att allt fungerar som förväntat, är du redo att distribuera paketet till alla äldre enheter.


## <a name="next-steps"></a>Nästa steg

* [Introduktion till hantering av enheter i Azure Active Directory](../device-management-introduction.md)



