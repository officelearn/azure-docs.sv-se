---
title: Aktivera Azure-prenumerationer och -konton | Microsoft Docs
description: Aktivera åtkomst med Azure Resource Manager-API:er för nya och befintliga konton och lös vanliga kontoproblem.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/27/2018
ms.topic: quickstart
ms.service: cost-management
manager: vitavor
ms.custom: ''
ms.openlocfilehash: f2cb5d33b8d7a7442da16a38e268c56de363a9c6
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274090"
---
# <a name="activate-azure-subscriptions-and-accounts-with-cloudyn"></a>Aktivera Azure-prenumerationer och konton med Cloudyn

Genom att lägga till eller uppdatera dina Azure Resource Manager-autentiseringsuppgifter kan Cloudyn identifiera alla konton och prenumerationer i din Azure-klientorganisation. Om du även har Azures diagnostiktillägg aktiverat på dina virtuella datorer kan Cloudyn samla in utökade mått som CPU och minne. Den här artikeln beskriver hur du aktiverar åtkomst med Azure Resource Manager-API:er för nya och befintliga konton. Här beskrivs även hur du löser vanliga kontoproblem.

Cloudyn har ingen åtkomst till flertalet av dina Azure-prenumerationsdata när prenumerationen är _inaktiverad_. Du måste redigera _inaktiverade_ konton så att Cloudyn kan komma åt dem.

## <a name="required-azure-permissions"></a>Azure-behörigheter som krävs

Specifika behörigheter krävs för att slutföra procedurerna i den här artikeln. Du eller din klientadministratör måste ha båda av följande behörigheter:

- Behörighet att registrera CloudynCollector-programmet i Azure AD-klientorganisationen.
- Möjligheten att tilldela programmet till en roll i Azure-prenumerationer.

I dina Azure-prenumerationer måste dina konton ha behörigheten `Microsoft.Authorization/*/Write` för att tilldela CloudynCollector-programmet. Den här åtgärden beviljas genom rollen [Ägare](../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator).

Om ditt konto har tilldelats rollen **Deltagare** har du inte tillräcklig behörighet för att tilldela programmet. Du får ett felmeddelande vid försök att tilldela CloudynCollector-programmet till din Azure-prenumeration.

### <a name="check-azure-active-directory-permissions"></a>Kontrollera Azure Active Directory-behörigheter

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** i Azure Portal.
3. Välj **Användarinställningar** i Azure Active Directory.
4. Kontrollera alternativet **Appregistreringar**.
    - Om det är inställt på **Ja** kan användare som inte är administratörer registrera AD-appar. Den här inställningen innebär att alla användare i Azure AD-klientorganisationen kan registrera en app.  
    ![Appregistreringar](./media/activate-subs-accounts/app-register.png)
    - Om alternativet **Appregistreringar** är inställt på **Nej** kan bara administratörsanvändare i klientorganisationen registrera Azure Active Directory-appar. Klientadministratören måste registrera CloudynCollector-programmet.


## <a name="add-an-account-or-update-a-subscription"></a>Lägg till ett konto eller uppdatera en prenumeration

När du lägger till en prenumeration till en kontouppdatering beviljar du åtkomst för Cloudyn till dina Azure-data.

### <a name="add-a-new-account-subscription"></a>Lägga till ett nytt konto (prenumeration)

1. Klicka på kugghjulssymbolen uppe till höger i Cloudyn-portalen och välj **Molnkonton**.
2. Klicka på **Lägg till nytt konto** så visas rutan **Lägg till nytt konto**. Ange informationen som krävs.  
    ![Rutan Lägg till nytt konto](./media/activate-subs-accounts//add-new-account.png)

### <a name="update-a-subscription"></a>Uppdatera en prenumeration

1. Om du vill uppdatera en _inaktiverad_ prenumeration som redan finns i Cloudyn går du till Kontohantering och klickar på pennsymbolen för redigering till höger om överordnad _klientorganisations-GUID_. Prenumerationerna är grupperade under en överordnad klientorganisation, så undvik att aktivera prenumerationer individuellt.
    ![Identifiera prenumerationer igen](./media/activate-subs-accounts/existing-sub.png)
2. Ange klient-ID om det behövs. Om du inte vet klient-ID kan du söka efter det genom att göra följande:
    1. Logga in på [Azure-portalen](https://portal.azure.com).
    2. Välj **Azure Active Directory** i Azure Portal.
    3. Om du vill hämta klientorganisations-ID:t väljer du **Egenskaper** för din Microsoft Azure Active Directory-klientorganisation.
    4. Kopiera katalog-ID GUID. Det här värdet är ditt klientorganisations-ID.
    Mer information finns i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
3. Välj pris-ID om det behövs. Om du inte vet pris-ID kan du söka efter det genom att göra följande.
    1. Uppe till höger i Azure Portal klickar du på din användarinformation och klickar sedan på **Visa min faktura**.
    2. Under **Faktureringskonto** klickar du på **Prenumerationer**.
    3. Under **Mina prenumerationer** väljer du prenumerationen.
    4. Ditt pris-ID visas under **ID för erbjudande**. Kopiera ID för erbjudande för prenumerationen.
4. I rutan Lägg till nytt konto (eller Redigera prenumeration) klickar du på **Spara** (eller **Nästa**). Du omdirigeras till Azure Portal.
5. Logga in på portalen. Klicka på **Acceptera** för att ge Cloudyn åtkomst till ditt Azure-konto.

    Du omdirigeras till sidan Kontohantering i Cloudyn och din prenumeration uppdateras med **aktiv** kontostatus. En grön bockmarkering bör visas under Resource Manager-kolumnen.

    Om du inte ser en grön bock för en eller flera av prenumerationerna betyder det att du inte har behörighet att skapa läsarappen (CloudynCollector) för prenumerationen. En användare med högre behörighet för prenumerationen måste upprepa den här processen.

Titta på videon [Connecting to Azure Resource Manager with Cloudyn](https://youtu.be/oCIwvfBB6kk) (Ansluta till Azure Resource Manager med Cloudyn), som går igenom processen.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Lösa vanliga indirekta Enterprise-konfigurationsproblem

När du använder Cloudyn-portalen första gången kan du se följande meddelanden, om du har ett Enterprise-avtal eller är en molnlösningsleverantör:

- *The specified API key is not a top level enrollment key* (Den angivna API-nyckeln är inte en registreringsnyckel på toppnivå) visas i **installationsguiden för Cloudyn**.
- *Direct Enrollment – No* (Direktregistrering – nej) visas i Enterprise-avtalsportalen.
- *No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account* (Inga användningsdata hittades för de senaste 30 dagarna. Kontakta återförsäljaren för att kontrollera om pålägg har aktiverats för ditt Azure-konto) visas i Cloudyn-portalen.

Föregående meddelanden indikerar att du har köpt ett Azure Enterprise-avtal genom en återförsäljare eller molntjänstleverantör. Återförsäljaren eller molntjänstleverantören måste aktivera _pålägg_ för ditt Azure-konto för att du ska kunna se dina data i Cloudyn.

Så här löser du problemen:

1. Återförsäljaren måste aktivera _pålägg_ för ditt konto. Mer information finns i [guiden för indirekt kundregistrering](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Du genererar Azure-nyckeln för Enterprise-avtal för användning med Cloudyn. Instruktioner finns i [Registrera ett Azure Enterprise-avtal och visa kostnadsdata](https://docs.microsoft.com/azure/cost-management/quick-register-ea).

Innan du kan generera API-nyckeln för Azures Enterprise-avtal för att konfigurera Cloudyn, måste du aktivera fakturerings-API:n för Azure genom att följa anvisningarna i:

- [Overview of Reporting APIs for Enterprise customers](../billing/billing-enterprise-api.md) (Översikt över rapporterings-API:er för Enterprise-kunder)
- [Microsoft Azure enterprise portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (Rapporterings-API för Microsoft Azure Enterprise Portal) under **Enabling data access to the API** (Aktivera dataåtkomst till API:et)

Du kanske även behöver ge avdelningsadministratörer, kontoägare och Enterprise-administratörer behörigheter att _visa debiteringar_ med fakturerings-API:et.

Det är bara Azure-tjänstadministratörer som kan aktivera Cloudyn. Det räcker inte att vara medadministratör. Du kan dock kringgå administratörskravet. Du kan begära att din Azure Active Directory-administratör beviljar dig behörighet att godkänna **CloudynAzureCollector** med ett PowerShell-skript. Följande skript ger behörighet att registrera tjänstens huvudnamn **CloudynAzureCollector** i Azure Active Directory.


```
#THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#Tenant - enter your tenant ID or Name
$tenant = "<ReplaceWithYourTenantID>"

#Cloudyn Collector application ID
$appId = "83e638ef-7885-479f-bbe8-9150acccdb3d"

#URL to activate the consent screen
$url = "https://login.windows.net/"+$tenant+"/oauth2/authorize?api-version=1&response_type=code&client_id="+$appId+"&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2FCloudynJava&prompt=consent"

#Choose your browser, the default is Internet Explorer

#Chrome
#[System.Diagnostics.Process]::Start("chrome.exe", "--incognito $url")

#Firefox
#[System.Diagnostics.Process]::Start("firefox.exe","-private-window $url" )

#IExplorer
[System.Diagnostics.Process]::Start("iexplore.exe","$url -private" )

```

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första självstudien för Cloudyn kan du läsa den i [Granska användning och kostnader](tutorial-review-usage.md).
