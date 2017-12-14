---
title: "Automatisk SaaS app användaretablering i Azure AD | Microsoft Docs"
description: "En introduktion till hur du kan använda Azure AD för att automatiskt etablera avetablera och kontinuerligt uppdatera användarkonton över flera SaaS-program från tredje part."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: cd82ef109abbc5707db4c02c2f14f9d55dfb74e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Vad är automatisk användaretablering för SaaS-appar?
Azure Active Directory (Azure AD) kan du automatisera skapandet, underhållet och borttagningen av användaridentiteter i molnet ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) program som Dropbox, Salesforce, ServiceNow med mera.

**Här följer några exempel på vad den här funktionen kan du göra:**

* Automatiskt skapa nya konton i de högra system för nya personer när de ansluter till din grupp eller organisation.
* Inaktivera automatiskt konton i de högra system när personer lämnar grupp eller organisation.
* Se till att identiteter i dina appar och -system hålls uppdaterade baserat på ändringar i katalogen, eller personal-system.
* Etablera icke-användare objekt, till exempel grupper för program som stöder dessa.

**Automatisk användaretablering innehåller även följande funktioner:**

* Möjligheten att matcha befintliga identiteter mellan käll- och system.
* Anpassningsalternativ i Azure AD-hjälpen passar aktuella konfigurationen av de system som din organisation använder och appar.
* Valfri e-postaviseringar för att etablera fel.
* Rapporterings- och aktivitet loggar för bättre övervakning och felsökning.

## <a name="why-use-automated-provisioning"></a>Varför använda Automatisk etablering?
Några vanliga motiveringen för att använda den här funktionen är:

* Att undvika kostnader, ineffektiviteter och handhavarfel vid manuell etablering processer.
* Att undvika kostnader i samband med värd och underhålla lösningar för anpassad utvecklat etablering och skript
* Att skydda din organisation genom att direkt ta bort användarens identitet från viktiga SaaS-appar när de lämnar organisationen.
* Importera enkelt bulk antalet användare i ett visst SaaS-program eller system.
* Om du vill få nyttan av din etablering köra lösningen från samma app åtkomstprinciper som du har definierat för Azure AD enkel inloggning.


## <a name="how-does-automatic-provisioning-work"></a>Hur fungerar automatisk etablering?
    
Den **Etableringstjänsten i Azure AD** etablerar du SaaS-appar och andra system, genom att ansluta till användaren hanterings-API-slutpunkter som tillhandahålls av leverantören av tillämpningsprogrammet. Dessa användare management API-slutpunkter kan Azure AD för att skapa, uppdatera och ta bort användare. För valda program tjänsten allokering kan också skapa, uppdatera och ta bort ytterligare identity-relaterade objekt, till exempel grupper och roller. 

![Etablering](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*bild 1: Azure AD etablering av tjänst*

![Utgående etablering](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*bild 2: ”utgående” användaretablering arbetsflödet från Azure AD populära SaaS-program*

![Inkommande etablering](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*bild 3: ”inkommande” arbetsflödet från populära mänsklig kapital Management (HCM) program till Azure Active Directory och Windows Server Active Directory för användaretablering*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Vilka program och system kan du använda med Azure AD automatisk användaretablering?

Azure AD-funktioner redan integrerade stöd för en mängd olika populära SaaS-appar och personal-system, samt allmänna stöd för appar som implementerar vissa delar av den [SCIM 2.0 standard](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

Alla ”aktuell” appar i Azure AD application gallery stöder automatisk användaretablering. [Lista över aktuella appar kan visas här.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

För ett program med stöd för automatisk användaretablering, måste det först ange nödvändiga användaren hanteringsslutpunkter som tillåter att externa program att automatisera skapande, underhåll och borttagning av användare. Därför inte alla SaaS-appar som är kompatibla med den här funktionen. Azure AD-Utvecklingsteamet kommer sedan att kunna skapa en allokering koppling till apparna för appar som har stöd för användarhantering API: er, och detta verk prioriteras av behoven hos aktuella och potentiella kunder. 

Kontakta Azure AD engineering team för att begära etablering support för ytterligare program, skicka ett meddelande via den [Azure Active Directory Feedbackforum](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning). 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hur ställer jag in automatisk etablering till ett program?

Konfigurationen av Azure AD etableras för ett valt program startar i den  **[Azure-portalen](https://potal.azure.com)**. I den **Azure Active Directory > företagsprogram** väljer **Lägg till**, sedan **alla**, och Lägg sedan till något av följande beroende på ditt scenario:

* Alla program i den **aktuella program** avsnittet stöd för automatisk etablering

* Använd alternativet ”icke-galleriet program” för anpassad utvecklat SCIM integreringar

![Galleri](./media/active-directory-saas-app-provisioning/gallery.png)

På skärmen application management etablering har konfigurerats i den **etablering** fliken.

![Inställningar](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **Administratörsautentiseringsuppgifter** måste anges till Azure AD etablering av tjänst som gör att den kan ansluta till Användarhantering API som tillhandahålls av programmet.

* **Attributet mappningar** kan konfigureras som anger vilka fält i källsystemet (exempel: Azure AD) kommer har innehållet synkroniseras till vilka fält i målsystemet (exempel: ServiceNow). Om målprogrammet stöder den kan i det här avsnittet du också konfigurera etablering av grupper förutom användarkonton. ”Matchande egenskaper” kan du välja vilka fält som används för att matcha konton mellan systemen. ”[Uttryck](active-directory-saas-writing-expressions-for-attribute-mappings.md)” gör att du kan ändra och transformera värdena som hämtas från källsystemet innan de skrivs till målsystemet. Mer information finns i [anpassa attributmappning](active-directory-saas-customizing-attribute-mappings.md).

![Inställningar](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Omfångsfilter** berätta tjänsten etablering för vilka användare och grupp i källsystemet ska etableras eller avetableras till målsystemet. Det finns två aspekter att Omfångsfilter som utvärderas ihop som bestämmer vem som ingår i omfånget för etablering:

* **Filtret på attributvärden** -”källa Objektområde”-menyn i attributet avbildningar kan filtrering på attributvärden. Du kan till exempel ange att endast användare med attributet ”avdelning” med ”försäljning” ska vara i omfånget för etablering.

* **Filtret på tilldelningar** -”Scope”-menyn i etableringen > Inställningar av portalen kan du ange om endast ”tilldelade” användare och grupper bör vara i omfånget för etablering eller om vara alla användare i Azure AD-katalog etableras. Mer information om ”tilldela” användare och grupper finns [tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Inställningar för** styr driften av tjänsten etablering för ett program, inklusive om den körs för tillfället eller inte.

* **Granskningsloggar** ange posterna för varje åtgärd som utförs av Azure AD etableras. Mer information finns i [etablering rapporteringsguide](active-directory-saas-provisioning-reporting.md).

![Inställningar](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>Vad som händer under etablering?

1. När du aktiverar etablering för ett program för första gången, utförs följande åtgärder:
   * Azure AD försöker matcha eventuella befintliga användare i SaaS-appen med sina motsvarande identiteter i katalogen. När en användare matchas, de är *inte* automatiskt aktiverad för enkel inloggning. För en användare får åtkomst till programmet, måste de uttryckligen tilldelas till appen i Azure AD antingen direkt eller via gruppmedlemskap.
   * Om du redan har angett vilka användare som ska tilldelas till programmet och Azure AD inte gick att hitta befintliga konton för dessa användare, etablera Azure AD nya konton för dem i programmet.
2. När den inledande synkroniseringen har slutförts enligt ovan, kommer Azure AD Kontrollera var tjugonde minut för följande ändringar:
   * Om nya användare har tilldelats till programmet (direkt eller via gruppmedlemskap), sedan de har etablerats med ett nytt konto i SaaS-app.
   * Om en användare har tagits bort och sedan sitt konto i SaaS-appen är markerade som inaktiverade (användarna aldrig fullständigt bort, vilket skyddar du förlorar data vid en felaktig konfiguration).
   * Om en användare nyligen har tilldelats till programmet och de redan har ett konto i SaaS-app, det kontot har markerats som aktiverad och vissa användaregenskaper kan uppdateras om de är inaktuella jämfört med katalogen.
   * Om en användares information (till exempel telefonnumret, arbetsplats) har ändrats i katalogen, kommer informationen också uppdateras i SaaS-program.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Hur ofta kan Azure AD för att skriva katalogändringar till SaaS-app?**

Efter en första fullständig synkronisering har slutförts, kontrollerar Azure AD etableras vanligtvis ändringar var tjugonde minut. 

Om appen SaaS returnerar flera fel (till exempel som i fallet med ogiltig administratörsautentiseringsuppgifter), kommer Azure AD gradvis att minska frekvensen på högst en gång per dag förrän felen har åtgärdats. När detta inträffar etableringsjobbet för den Azure AD hamnar i tillståndet ”karantän” och anger detta i den [etablering sammanfattningsrapport](active-directory-saas-provisioning-reporting.md).

**Hur lång tid tar det för att etablera Mina användare?**

När en första fullständig synkronisering har slutförts kan inträffa inkrementella ändringar i 20 – 40 minuter. Om du försöker att etablera de flesta av din katalog sedan beror det på antalet användare och grupper som du har. Prestanda är beroende av prestandan för användarhantering API: er som provisioning services använder för att läsa data från källsystemet och skriva data till målsystemet. 

Prestanda är också längre tid om det finns många (registreras i den [granskningsloggar](active-directory-saas-provisioning-reporting.md)) och tjänsten etablering är i tillståndet ”karantän”.

**Vad är en första fullständig synkronisering och varför det tar längre tid än efterföljande synkroniseringar?**

Första gången Azure AD Etablerar-tjänsten körs för en viss app det tar en ”ögonblicksbild” användare (och grupper eventuellt) i källkatalogen. Den här ögonblicksbilden gör tjänsten etablering för att minska antalet sändningar till käll- och API: er och att efterföljande ”delta” synkroniseringar fungerar på ett mer effektivt. 

Den första fullständiga synkroniseringen av användare ofta kan utföras i minuter för mycket små kataloger, men det kan ta flera timmar för större kataloger. Enterprise-kataloger med hundratals tusentals användare kan ta många timmar för den inledande synkroniseringen att slutföra. Efter den första synkroniseringen inträffar efterföljande ”delta” synkroniseringar emellertid mycket snabbare.

> [!NOTE]
> För program som har stöd för etablering av grupper och gruppmedlemskap, ökar om du aktiverar detta kraftigt den tid det tar för en fullständig synkronisering ska slutföras. Om du inte vill att etablera gruppnamn och gruppmedlemskap i ditt program, kan du inaktivera detta i den [attributet mappningar](active-directory-saas-customizing-attribute-mappings.md) av etablering konfigurationen.

**Hur kan jag spåra förloppet för den aktuella Etableringsjobbet?**

Finns det [etablering rapporteringsguide](active-directory-saas-provisioning-reporting.md).

**Hur vet jag om användare ska kunna hämta etablerats korrekt?**

Alla fel registreras i Azure AD granskningsloggar. Mer information finns i [etablering rapporteringsguide](active-directory-saas-provisioning-reporting.md).

**Hur kan jag skicka feedback till teknikteamet?**

Kontakta oss via den [Azure Active Directory Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Anpassa attributmappning för Användaretablering](active-directory-saas-customizing-attribute-mappings.md)
* [Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Omfångsfilter för Användaretablering](active-directory-saas-scoping-filters.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md)
* [Kontot etablering meddelanden](active-directory-saas-account-provisioning-notifications.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)

