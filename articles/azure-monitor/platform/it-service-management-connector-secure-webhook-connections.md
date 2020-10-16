---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i Azure Monitor
description: Den här artikeln visar hur du ansluter dina ITSM-produkter/-tjänster med säker export i Azure Monitor för att centralt övervaka och hantera ITSM-arbetsobjekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: bf68963515e1208868efb40c2d3fc56c9ab4e0df
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107767"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Ansluta Azure till ITSM-verktyg med säker export

Den här artikeln visar hur du konfigurerar anslutningen mellan din ITSM-produkt (IT Service Management) eller tjänst med säker export.

Säker export är en uppdaterad version av [anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC)](./itsmc-overview.md). Med båda versionerna kan du skapa arbets objekt i ett ITSM-verktyg när Azure Monitor skickar aviseringar. Funktionerna omfattar statistik, logg och aktivitets logg aviseringar.

ITSMC använder autentiseringsuppgifter för användar namn och lösen ord. Säker export har starkare autentisering eftersom den använder Azure Active Directory (Azure AD). Azure AD är Microsofts molnbaserade hanteringstjänst för identiteter och åtkomst. Det hjälper användarna att logga in och komma åt interna eller externa resurser. Med hjälp av Azure AD med ITSM kan du identifiera Azure-aviseringar (via Azure AD-programid: t) som skickades till det externa systemet.

> [!NOTE]
> Möjligheten att ansluta Azure till ITSM-verktyg med hjälp av säker export är i för hands version.

## <a name="secure-export-architecture"></a>Säker export arkitektur

Den säkra export arkitekturen introducerar följande nya funktioner:

* **Ny åtgärds grupp**: aviseringar skickas till verktyget ITSM via åtgärds gruppen säker webhook, i stället för ITSM-som ITSMC använder.
* **Azure AD-autentisering**: autentisering sker via Azure AD i stället för autentiseringsuppgifter för användar namn/lösen ord.

## <a name="secure-export-data-flow"></a>Data flöde för säker export

Stegen i data flödet för säker export är:

1. Azure Monitor skickar en avisering som är konfigurerad för att använda säker export.
1. Aviserings nytto lasten skickas via en säker webhook-åtgärd till ITSM-verktyget.
1. ITSM-programmet kontrollerar med Azure AD om aviseringen har behörighet att ange ITSM-verktyget.
1. Om aviseringen är auktoriserad kan programmet:
   
   1. Skapar ett arbets objekt (till exempel en incident) i ITSM-verktyget.
   1. Binder ID: t för konfigurationsobjektet (CI) till Customer Management-databasen (CMDB).

![Diagram som visar hur verktyget ITSM kommunicerar med Azure A D, Azure-aviseringar och en åtgärds grupp.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Anslutning med BMC-Helix

Säker export stöder BMC-Helix. Några av fördelarna med integrationen är:

* **Bättre autentisering**: Azure AD ger mer säker autentisering utan tids gränser som vanligt vis uppstår i ITSMC.
* **Aviseringar som löses i ITSM-verktyget**: mått varningar implementerar "utlöst" och "löst" tillstånd. När villkoret är uppfyllt är aviserings tillståndet "utlöst". När villkoret inte uppfylls längre är aviserings tillståndet "löst". I ITSMC kan aviseringar inte lösas automatiskt. Med säker export flödar det lösta tillstånd till ITSM-verktyget och uppdateras automatiskt.
* **[Vanligt aviserings schema](./alerts-common-schema.md)**: i ITSMC varierar schemat för aviserings nytto lasten baserat på aviserings typen. I säker export finns det ett gemensamt schema för alla aviserings typer. Det här gemensamma schemat innehåller CI för alla aviserings typer. Alla aviserings typer kan binda sitt CI med CMDB.

Börja använda ITSM-anslutningsprogram-verktyget med följande steg:

1. Registrera appen med Azure AD.
2. Skapa en säker webhook-åtgärds grupp.
3. Konfigurera din partner miljö.

## <a name="register-with-azure-active-directory"></a>Registrera dig för Azure Active Directory

Följ de här stegen för att registrera programmet med Azure AD:

1. Följ stegen i [Registrera ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md).
1. I Azure AD väljer du **exponera program**.
1. Välj **set** för **program-ID-URI**.

   [![Skärm bild av alternativet för att ställa in U R i för programmet I D.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
1. Välj **Spara**.

## <a name="create-a-secure-webhook-action-group"></a>Skapa en säker webhook-åtgärds grupp

När programmet har registrerats med Azure AD kan du skapa arbets objekt i ITSM-verktyget baserat på Azure-aviseringar med hjälp av åtgärden säker webhook i åtgärds grupper.

Åtgärds grupper ger ett modulärt och återanvändbart sätt att utlösa åtgärder för Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Azure Log Analytics-aviseringar i Azure Portal.
Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper i Azure Portal](./action-groups.md).

Använd följande procedur i BMC Helix-miljön:

1. Logga in på integration Studio.
1. Sök efter **varnings flödet Skapa incident från Azure** .
1. Kopiera webhook-URL: en.
   
   ![Skärm bild av webhooken U R L i integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Om du vill lägga till en webhook till en åtgärd, följer du dessa anvisningar för säker webhook:

1. Sök efter och välj **övervaka**i [Azure Portal](https://portal.azure.com/). I **övervaknings** fönstret samlas alla övervaknings inställningar och data i en vy.
1. Välj **aviseringar**  >  **Hantera åtgärder**.
1. Välj [Lägg till åtgärds grupp](./action-groups.md#create-an-action-group-by-using-the-azure-portal)och fyll i fälten.
1. Ange ett namn i rutan **Åtgärds grupp namn** och ange ett namn i rutan **kort namn** . Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.
1. Välj **säker webhook**.
1. Välj följande information:
   1. Välj objekt-ID för den Azure Active Directory instans som du har registrerat.
   1. För URI: n klistrar du in webhook-URL: en som du kopierade från BMC Helix-miljön.
   1. Ange **aktivera det gemensamma aviserings schemat** till **Ja**. 

   Följande bild visar konfigurationen av en exempel säker webhook-åtgärd:

   ![Skärm bild som visar en säker webhook-åtgärd.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-partner-environment"></a>Konfigurera partner miljön

### <a name="connect-bmc-helix-to-azure-monitor"></a>Ansluta BMC-Helix till Azure Monitor

Följande avsnitt innehåller information om hur du ansluter din BMC Helix-produkt och säker export i Azure.

### <a name="prerequisites"></a>Krav

Se till att du uppfyller följande krav:

* Azure AD har registrerats.
* Du har den version av BMC Helix som stöds för flera moln tjänster (version 19,08 eller senare).

### <a name="configure-the-bmc-helix-connection"></a>Konfigurera BMC Helix-anslutningen

1. Följ anvisningarna accoring till versionen:
   * [Aktivera inbyggd integrering med Azure Monitor för version 20,02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Aktivera inbyggd integrering med Azure Monitor för version 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

1. Som en del av konfigurationen av anslutningen i BMC Helix, går du till integrations-BMC-instansen och följer de här anvisningarna:

   1. Välj **katalog**.
   1. Välj **Azure-aviseringar**.
   1. Välj **kopplingar**.
   1. Välj **konfiguration**.
   1. Välj **Lägg till ny anslutnings** konfiguration.
   1. Fyll i informationen för konfigurations avsnittet:
      - **Namn**: skapa egna.
      - **Typ av auktorisering**: **ingen**
      - **Beskrivning**: skapa en egen.
      - **Webbplats**: **moln**
      - **Antal instanser**: **2**, standardvärdet.
      - **Kontrol lera**: är markerat som standard för att aktivera användning.
      - ID för Azure-klient organisation och Azure-program hämtas från det program som du definierade tidigare.

![Skärm bild som visar BMC-konfiguration.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-overview.md)