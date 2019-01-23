---
title: Kontrollera hybrid Azure AD-anslutning av dina enheter | Microsoft Docs
description: Lär dig hur du styr hybrid Azure AD join för dina enheter i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
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
ms.openlocfilehash: fd6da7e0c350ef7da02b885973f1386edc7cbc48
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451433"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Kontrollera Azure AD-anslutningshybriden för dina enheter

Hybrid Azure Active Directory (Azure AD)-anslutning är en process för att automatiskt registrera dina lokala domänanslutna enheter med Azure AD. Det finns fall där du inte vill att alla dina enheter registreras automatiskt. Detta gäller, till exempel under den inledande distributionen för att kontrollera att allt fungerar som förväntat.

Den här artikeln innehåller råd om hur du kan styra hybrid Azure AD join för dina enheter. 


## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med:

-  [Introduktion till enhetshantering i Azure Active Directory](../device-management-introduction.md)
 
-  [Planera implementeringen av Azure Active Directory-hybridanslutning](hybrid-azuread-join-plan.md)

-  [Konfigurera hybrid Azure Active Directory-anslutning för hanterade domäner](hybrid-azuread-join-managed-domains.md) eller [konfigurera hybrid Azure Active Directory-anslutning för federerade domäner](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Styra aktuella Windows-enheter

För enheter som kör Windows operativsystem, versionen som stöds är Windows 10 Anniversary Update (version 1607) eller senare. Ett bra tips är att uppgradera till den senaste versionen av Windows 10.

Alla Windows befintliga enheter automatiskt registrera med Azure AD vid starten av enheten eller användaren logga in. Du kan styra detta genom att använda ett grupprincipobjekt (GPO) eller System Center Configuration Manager.

För att styra aktuella Windows-enheter, måste du: 


1.  **Till alla enheter**: Inaktivera automatisk enhetsregistrering.
2.  **Till valda enheter**: Aktivera automatisk enhetsregistrering.

När du har kontrollerat att allt fungerar som förväntat, är du redo att aktivera automatisk enhetsregistrering för alla enheter igen.



### <a name="group-policy-object"></a>Grupprincipobjekt 

Du kan kontrollera enhetsbeteende för registrering av dina enheter genom att distribuera följande grupprincipobjekt: **Registrera domänanslutna datorer som enheter**.

Så här Grupprincipobjektet:

1.  Öppna **Serverhanteraren**, och gå till **verktyg** > **Group Policy Management**.

2.  Gå till domännoden som motsvarar den domän där du vill inaktivera eller aktivera automatisk registrering.

3.  Högerklicka på **grupprincipobjekt**, och välj sedan **New**.

4.  Ange ett namn (till exempel **Hybrid Azure AD-anslutning**) för din grupprincipobjekt. 

5.  Välj **OK**.

6.  Högerklicka på ditt nya GPO och välj sedan **redigera**.

7.  Gå till **Datorkonfiguration** > **principer** > **Administrationsmallar** > **Windows Komponenter** > **Enhetsregistrering**. 

8.  Högerklicka på **registrera domänanslutna datorer som enheter**, och välj sedan **redigera**.

    > [!NOTE] 
    > Den här mallen har ändrats från tidigare versioner av konsolen Grupprinciphantering. Om du använder en tidigare version av konsolen går du till **Datorkonfiguration** > **principer** > **Administrationsmallar**  >  **Windows-komponenter** > **Arbetsplatsanslutning** > **automatiskt workplace join-klientdatorer**. 

9.  Välj något av följande inställningar och välj sedan **tillämpa**:

    - **Inaktiverad**: Att förhindra automatisk enhetsregistrering.
    - **Aktiverad**: Att aktivera automatisk enhetsregistrering.

10. Välj **OK**.

Du måste länka Grupprincipobjektet till en valfri plats. Till exempel ange den här principen för alla domänanslutna aktuella enheter i din organisation och länka Grupprincipobjektet till domänen. Gör en kontrollerad distribution genom att ställa in principen till domänanslutna Windows befintliga enheter som hör till en organisationsenhet eller en säkerhetsgrupp.

### <a name="configuration-manager-controlled-deployment"></a>Configuration Manager kontrollerad distribution 

Du kan styra beteendet enheten registreringen av din befintliga enheter genom att konfigurera följande klientinställning: **Registrera automatiskt nya Windows 10 domänanslutna enheter med Azure Active Directory**.

Konfigurera klientinställningen:

1.  Öppna **Configuration Manager**, och gå till **molntjänster**.

2.  Under **Enhetsinställningar**, väljer du något av följande inställningar för **automatiskt registrera nya Windows 10 domänanslutna enheter med Azure Active Directory**:

    - **Inte**: Att förhindra automatisk enhetsregistrering.
    - **Ja**: Att aktivera automatisk enhetsregistrering.


3.  Välj **OK**.
    

Du måste länka den här klientinställningen till en valfri plats. Till exempel för att konfigurera den här klientinställningen för alla aktuella Windows-enheter i din organisation, länka klientinställningen till domänen. Du kan konfigurera för klienten att domänanslutna Windows befintliga enheter som hör till en organisationsenhet eller en säkerhetsgrupp om du vill göra en kontrollerad distribution.

> [!Important]
> Även om ovanstående konfiguration tar hand om befintliga domänanslutna Windows 10-enheter, enheter som nyligen ansluter till domänen kan fortfarande försöka slutföra hybrid Azure AD-anslutning på grund av möjlig fördröjning vid tillämpning av en grupprincip eller Configuration Manager-inställningar på enheter. 
>
> Om du vill undvika detta rekommenderar vi att du skapar en ny Sysprep-bild (används som exempel för en metod för etablering). Skapa det från en enhet som aldrig tidigare hybrid Azure AD-anslutna och att redan har en grupprincip ställde eller Configuration Manager-klientinställningen tillämpas. Du måste också använda den nya avbildningen för att etablera nya datorer som ansluter till organisationens domän. 

## <a name="control-windows-down-level-devices"></a>Kontrollera äldre Windows-enheter

Om du vill registrera Windows äldre enheter, som du behöver hämta och installera Windows Installer-paketet (.msi) från Download Center på den [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554) sidan.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Paketet stöder alternativen standard tyst installation med parametern tyst. Den aktuella grenen av Configuration Manager ger fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarens kontext. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften kopplar tyst enhet med Azure AD med autentiseringsuppgifterna för användaren när de har autentiserat med Azure AD.

För att styra enhetsregistreringen, bör du distribuera Windows Installer-paketet enbart för en grupp av Windows äldre enheter. Om du har kontrollerat att allt fungerar som förväntat, är du redo att distribuera paketet till alla äldre enheter.


## <a name="next-steps"></a>Nästa steg

* [Introduktion till enhetshantering i Azure Active Directory](../device-management-introduction.md)



