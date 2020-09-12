---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i Azure Monitor
description: Den här artikeln innehåller information om hur du ansluter dina ITSM-produkter/-tjänster med säker export i Azure Monitor för att centralt övervaka och hantera ITSM-arbetsobjekten.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568650"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Ansluta Azure till ITSM-verktyg med säker export

Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM-produkt/-tjänst med säker export.

Säker export är och uppdaterad version av [ITSMC](./itsmc-overview.md) (IT-Tjänstehantering). Med båda versionerna kan du skapa arbets objekt i ett ITSM-verktyg när Azure Monitor-aviseringar utlöses. Funktionerna omfattar statistik, logg-och aktivitets logg aviseringar.

[ITSMC](./itsmc-overview.md) använder autentiseringsuppgifter för användare och lösen ord, medan säker export har starkare autentisering eftersom den använder Azure Active Directory (Azure AD). Azure Active Directory (Azure AD) är Microsofts molnbaserade identitets- och åtkomshanteringstjänst. Det hjälper användarna att logga in och komma åt interna eller externa resurser. Med hjälp av Azure AD med ITSM kan du identifiera Azure-aviseringar (med Azure AD-programid: t) som skickades till det externa systemet.

> [!NOTE]
> Verktygen för att ansluta Azure till ITSM med säker export är i för hands version

## <a name="secure-export-architecture"></a>Säker export arkitektur

Den säkra export arkitekturen introducerar följande nya funktioner:

* **Ny åtgärds grupp** – aviseringar skickas till ITSM-verktyget med hjälp av åtgärds gruppen säker webhook (i stället för ITSM-åtgärds grupp med i ITSMC).
* **Azure AD-autentisering** – autentisering sker med hjälp av Azure AD i stället för autentiseringsuppgifter för användare/lösen ord.

## <a name="secure-export-data-flow"></a>Data flöde för säker export

Stegen för data flödet för säker export är:

1) En avisering som kon figurer ATS för att använda säker export utlöses i Azure Monitor
2) Aviseringens nytto Last skickas via en säker webhook-åtgärd till ITSM-verktyget.
3) ITSM-programmet kontrollerar med Azure AD om aviseringen har behörighet att ange ITSM-verktyget.
4) Om aviseringen har behörighet för programmet:
    1) Skapar ett arbets objekt (t. ex. incident) i ITSM-verktyget.
    2) Binder konfigurations objektets ID (CI) till kund hanterings databasen (CMDB).
![ITSM-diagram](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Anslutning med BMC-Helix

Säker export stöder BMC-Helix. Några av fördelarna med integrationen är:

* **Bättre autentisering** – Azure AD ger mer säker autentisering utan tids gränser som vanligt vis uppstår i ITSMC.
* **Aviseringar som lösts i ITSM-verktyget** – mått varningar implementerar status "utlöst" och "löst". När villkoret är uppfyllt är aviserings tillståndet "utlöst". När villkoret inte uppfylls längre är aviserings tillståndet "löst". I ITSMC kunde aviseringar inte lösas automatiskt. Med säker export flödar det lösta tillstånd till ITSM-verktyget och uppdateras automatiskt.
* **[Gemensamt schema tillåter](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** – i ITSMC, kan schemat för aviserings nytto lasten variera beroende på aviserings typen. I säker export är vi ett gemensamt schema för alla aviserings typer. Det nya gemensamma schemat innehåller CI för alla aviserings typer. Det innebär att alla aviserings typer kan binda sitt CI med CMDB.

Börja använda ITSM-anslutningsprogram med följande steg:

1. Registrera din app med Azure Active Directory.
2. Skapa en säker webhook-åtgärds grupp.
3. Konfigurera din partner miljö.

## <a name="register-with-azure-active-directory"></a>Registrera dig för Azure Active Directory

Följ de här stegen för att registrera Azure AD-program med Azure Active Directory

1) [Azure AD-skapande](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) I Azure Active Directory väljer du "exponera program"
3) Välj Set i program-ID URI [ ![ Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4) Klicka på Spara.

## <a name="create-a-secure-webhook-action-group"></a>Skapa en säker webhook-åtgärds grupp

När du har registrerat din Azure AD kan du skapa arbets objekt i ITSM-verktyget baserat på Azure-aviseringar med hjälp av åtgärden säker webhook i åtgärds grupper.
Med åtgärds grupper får du ett modulärt och återanvändbart sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Azure Log Analytics varningar i Azure Portal.
Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper i Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
Följ dessa steg:

I BMC Helix-miljö:

1. Logga in på integration Studio.
2. Sök efter varnings flödet Skapa incident från Azure.
3. Kopiera webhook-URL: en.
![BMC-URL](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Om du vill lägga till en webhook till en åtgärd följer du anvisningarna för en säker webhook:

1. Sök efter och välj **övervaka**i [Azure Portal](https://portal.azure.com/). I **övervaknings** fönstret samlas alla övervaknings inställningar och data i en vy.
2. Välj **Aviseringar** och sedan **Hantera åtgärder**.
3. Välj [Lägg till åtgärds grupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)och fyll i fälten.
4. Ange ett namn i rutan **Åtgärds grupp namn** och ange ett namn i rutan **kort namn** . Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.
5. Välj **säker webhook**
6. Välj Redigera information. Följande bild visar ett exempel på en skyddad webhook-åtgärd:
    1. Välj rätt objekt-ID för den Azure Active Directory som du registrerade
    2. Klistra in URL: en för webhook som du kopierade från "BMC Helix-miljön" i fältet URI
    3. Ange ett **vanligt aviserings schema** till **Ja**. 
7. Följande bild visar en exempel på säker konfiguration av webhook-åtgärd: ![ säker webhook](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Konfigurera partner miljö

### <a name="connect-bmc-helix-to-azure-monitor"></a>Ansluta BMC-Helix till Azure Monitor

Följande avsnitt innehåller information om hur du ansluter din BMC Helix-produkt och säker export i Azure.

### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:

* Azure AD har registrerats.
* Den version av BMC-Helix som stöds för flera moln tjänster: 20,02-version eller senare

Så här konfigurerar du BMC Helix-anslutningen:

1) [Aktivera inbyggd integrering med Azure Monitor för version 20,2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) Som en del av konfigurationen av anslutningen i BMC-Helix, går du till integrations-BMC-instansen och följer anvisningarna:

1. Välj **katalog**
2. Välj **Azure-aviseringar**
3. Välj **kopplingar**
4. Välj **konfiguration**
5. Välj **Lägg till ny anslutnings** konfiguration
6. Fyll i informationen för konfigurations avsnittet.
    1. **Namn** – skapa egna
    2. **Typ av auktorisering** – ingen
    3. **Beskrivning**– skapa en egen
    4. **Plats**– moln
    5. **Antal instanser** -2 – standardvärde
    6. **Check** -väljs som standard och aktivera användning
    7. Azure-klient-ID, Azure-program-ID hämtas från programmet som definierades i steget "skapat Azure Active Directory".
![BMC-konfiguration](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
