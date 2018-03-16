---
title: "Hur du använder åtkomstkontroll (Java) | Microsoft Docs"
description: "Lär dig hur du utvecklar och använder åtkomstkontroll med Java i Azure."
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mtillman
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: b555ef40fae8156d2957643697d6450ef22b215a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Hur du autentiserar användare webbprogram med Azure Access Control Service med Eclipse
Den här guiden visar hur du använder Azure Access Control Service (ACS) i Azure-verktygen för Eclipse. Mer information om ACS finns i [nästa steg](#next_steps) avsnitt.

> [!NOTE]
> Azure Access tjänstfilter kontrollen är en community technology preview. Som förhandsversionen stöds den formellt inte av Microsoft.
> 
> 

## <a name="what-is-acs"></a>Vad är ACS?
De flesta utvecklare är inte identitet experter och normalt vill inte ägna tid utveckla autentisering och auktorisering mekanismer för sina program och tjänster. ACS är en Azure-tjänst som tillhandahåller ett enkelt sätt att autentisera användare som behöver åtkomst till ditt webbprogram och tjänster utan att behöva faktor komplexa autentiseringslogiken i koden.

Följande funktioner är tillgängliga i ACS:

* Integrering med Windows Identity Foundation (WIF).
* Stöd för populära web identitetsleverantörer (IP-adresser) inklusive Windows Live ID, Google, Yahoo! och Facebook.
* Stöd för Active Directory Federation Services (AD FS) 2.0.
* En Open Data Protocol (OData)-baserade management-tjänst som ger programmatisk åtkomst till ACS-inställningar.
* En hanteringsportal som tillåter administrativ åtkomst till ACS-inställningar.

Mer information om ACS finns [Access Control Service 2.0][Access Control Service 2.0].

## <a name="concepts"></a>Begrepp
Azure ACS bygger på säkerhetsobjekt anspråksbaserad identitet - en konsekvent metod för att skapa autentiseringsmekanismer för program som körs lokalt eller i molnet. Anspråksbaserad identitet innehåller ett vanligt sätt för program och tjänster att hämta identitetsinformation de behöver om användarna i organisationen i andra organisationer och på Internet.

Om du vill utföra åtgärderna i den här guiden bör du förstå följande begrepp:

**Klienten** -i kontexten för den här instruktioner är en webbläsare som försöker få åtkomst till ditt webbprogram.

**Programmet för förlitande part (RP)** -en RP programmet är en webbplats eller tjänst som outsources autentisering till en extern utfärdare. Identitet specialanpassat innebär att RP förtroenden utfärdaren. Den här guiden förklarar hur du konfigurerar ditt program för ACS-förtroende.

**Token** -token är en samling säkerhetsdata som utfärdas vanligtvis vid autentiseringen för en användare. Den innehåller en uppsättning *anspråk*, attribut på den autentiserade användaren. Ett anspråk kan representera ett användarnamn, en identifierare för en roll som en användare som hör till en användares ålder och så vidare. En token är vanligtvis digitalt signerad, vilket innebär att det alltid kan hämtas tillbaka till dess utfärdare och dess innehåll inte kan brytas. En användare får åtkomst till ett RP-program genom att presentera en giltig token som utfärdas av en certifikatutfärdare som litar på RP-programmet.

**Identitet providerns IP-** -en IP är en certifikatutfärdare som autentiserar användaridentiteter och utfärdar säkerhetstoken. Det faktiska arbetet för att utfärda token har implementerats även om en särskild tjänsten som kallas säkerhet säkerhetstokentjänst (STS). Vanliga exempel på IP-adresser är Windows Live ID, Facebook, business användaren databaser (till exempel Active Directory) och så vidare.
När ACS har konfigurerats för att lita på en IP-adress, acceptera och validera token som utfärdats av denna IP. ACS kan lita på flera IP-adresser samtidigt, vilket innebär att när ACS har förtroende för ditt program, kan du direkt erbjuda ditt program för alla autentiserade användare från alla IP-adresser att ACS förtroenden för din räkning.

**Federation Provider (RP)** -IP-adresser har direkt kunskaper om användare, och autentisera dem med hjälp av sina autentiseringsuppgifter och ge ut anspråk om att de vet om dem. En Federation Provider (RP) är en annan typ av utfärdare: i stället för att autentisera användare direkt det fungerar som en mellanhand och mäklare autentisering mellan en RP och en eller flera IP-adresser. Både IP-adresser och FPs utfärda säkerhetstoken, därför båda använder säkerhet Token tjänster (STS). ACS är en RP.

**ACS regeln motorn** -logik som används för att omvandla inkommande token från tillförlitliga IP-adresser till token som är avsedd att användas av RP fastställs i form av enkla anspråk omvandling regler. ACS har en motor för affärslogik som tar hand om tillämpa oavsett omvandling logik som angetts för din RP.

**ACS-Namespace** -en namnrymd är en toppnivåpartition av ACS som du använder för att organisera dina inställningar. Ett namnområde innehåller en lista över IP-adresser du litar på RP-program som du vill ska fungera, regler som du förväntar dig regeln motorn för att bearbeta inkommande token med och så vidare. Ett namnområde visar olika slutpunkter som ska användas av programmet och utvecklare för att hämta ACS att utföra sitt arbete.

Följande bild visar hur ACS-autentisering fungerar med ett webbprogram:

![Flödesdiagram för ACS][acs_flow]

1. (I det här fallet en webbläsare) klienten begär en sida från RP.
2. Eftersom begäran inte ännu autentiseras, RP omdirigeras användaren till utfärdaren som litar, vilket är ACS. ACS ger användaren valet av IP-adresser som har angetts för den här RP. Användaren väljer rätt IP-Adressen.
3. Klienten bläddrar till den IP-autentiseringssidan och användaren uppmanas att logga in.
4. När klienten har autentiserats (t.ex, identiteten autentiseringsuppgifter har angetts), utfärdar IP-Adressen en säkerhetstoken.
5. När du utfärdar en säkerhetstoken IP dirigerar om klienten till ACS och klienten skickar en säkerhetstoken som utfärdas av IP-Adressen för ACS.
6. ACS verifierar säkerhetstoken som utfärdats av IP-Adressen, indata identiteten anspråk i den här variabeln till ACS-regelmotor beräknar identitetsanspråk utdata och skickar en ny säkerhetstoken som innehåller dessa utgående anspråk.
7. ACS dirigerar om klienten till RP. Klienten skickar nya säkerhetstoken som utfärdats av ACS till RP. RP verifierar signaturen på den säkerhetstoken som utfärdats av ACS validerar anspråk i denna token och returnerar sidan som ursprungligen begärdes.

## <a name="prerequisites"></a>Förutsättningar
Om du vill utföra åtgärderna i den här guiden behöver du följande:

* En Java Developer Kit (JDK), v 1.6 eller senare.
* Solförmörkelse IDE för Java EE-utvecklare Indigo eller senare. Detta kan hämtas från <http://www.eclipse.org/downloads/>. 
* En distribution av en Java-baserad webbserver eller programservern, till exempel Apache Tomcat GlassFish, JBoss-programservern eller Jetty.
* en Azure-prenumeration som kan erhållas från <http://www.microsoft.com/windowsazure/offers/>.
* Azure-verktygen för Eclipse April 2014-versionen eller senare. Mer information finns i [installera Azure Toolkit för Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Ett X.509-certifikat ska användas med ditt program. Du behöver det här certifikatet i både offentliga certifikatets (.cer) och Personal Information Exchange (. PFX)-format. (Alternativ för att skapa detta certifikat beskrivs senare i den här självstudiekursen).
* Om du är bekant med Azure compute emulator och distribution av tekniker som beskrivs i [skapar en Hello World program för Azure i Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Skapa en ACS-Namespace
Om du vill börja använda Access Control Service (ACS) i Azure, måste du skapa ett namnområde för ACS. Namnområdet ger ett unikt scope för adressering ACS resurser i ditt program.

1. Logga in på den [Azure-hanteringsportalen][Azure Management Portal].
2. Klicka på **Active Directory**. 
3. Klicka för att skapa ett nytt namnområde för åtkomstkontroll **ny**, klickar du på **Apptjänster**, klickar du på **åtkomstkontroll**, och klicka sedan på **Snabbregistrering**. 
4. Ange ett namn för namnområdet. Azure verifierar att namnet är unikt.
5. Välj den region där namnområdet används. För bästa prestanda använder du den region där du distribuerar ditt program.
6. Om du har mer än en prenumeration väljer du den prenumeration som du vill använda för ACS-namnområdet.
7. Klicka på **Skapa**.

Azure skapar och aktiverar namnområdet. Vänta tills status för det nya namnområdet är **Active** innan du fortsätter. 

## <a name="add-identity-providers"></a>Lägg till identitetsleverantörer
I den här uppgiften att lägga till IP-adresser ska användas med RP-program för autentisering. Den här uppgiften visar hur du lägger till Windows Live som en IP i exempelsyfte, men du kan använda någon av IP-adresser som anges i ACS-hanteringsportalen.

1. I den [Azure-hanteringsportalen][Azure Management Portal], klickar du på **Active Directory**, Välj ett namnområde för åtkomstkontroll och klicka sedan på **hantera**. ACS-hanteringsportalen öppnas.
2. I det vänstra navigeringsfönstret i ACS-hanteringsportalen, klickar du på **identitetsleverantörer**.
3. Windows Live ID är aktiverad som standard och kan inte tas bort. För tillämpning av den här kursen används bara Windows Live ID. Den här skärmen är dock där du kan lägga till andra IP-adresser genom att klicka på den **Lägg till** knappen.

Windows Live ID är nu aktiverad som en IP-adress för ACS-namnområdet. Därefter måste du ange Java webbprogrammet (skapas senare) som en RP.

## <a name="add-a-relying-party-application"></a>Lägg till en förlitande partsprogram
I den här uppgiften kan du konfigurera ACS för att identifiera ditt webbprogram för Java som ett giltigt RP-program.

1. Klicka på hanteringsportalen ACS **förlitande partsprogram**.
2. På den **förlitande partsprogram** klickar du på **Lägg till**.
3. På den **Lägg till förlitande partsprogram** gör följande:
   
   1. I **namn**, skriver du namnet på RP. Syftet med den här kursen skriver **Azure Web App**.
   2. I **läge**väljer **ange inställningar manuellt**.
   3. I **sfär**, ange URI som gäller för säkerhetstoken som utfärdats av ACS. Den här uppgiften skriver **http://localhost: 8080 /**.
      ![Förlitande part sfär för användning i beräkningsemulatorn][relying_party_realm_emulator]
   4. I **Retur-URL** ange Webbadressen som returnerar ACS säkerhetstoken. Den här uppgiften skriver **http://localhost:8080/MyACSHelloWorld/index.jsp**
      ![förlitande part Retur-URL för användning i beräkningsemulatorn][relying_party_return_url_emulator]
   5. Godkänn standardvärdena i resten av fälten.
4. Klicka på **Spara**.

Du har nu konfigurerat ditt webbprogram för Java när den körs i Azure-beräkningsemulatorn (på http://localhost: 8080 /) ska vara en RP i ACS-namnområdet. Skapa sedan de regler som ACS använder för att bearbeta anspråk för RP.

## <a name="create-rules"></a>Skapa regler
I den här uppgiften definierar de regler som driver hur anspråk skickas från IP-adresser till din RP. I den här guiden kommer vi bara konfigurera ACS om du vill kopiera inkommande anspråkstyper och värden utan filtrering eller ändra dem direkt i utdatatoken.

1. Klicka på huvudsidan i ACS-hanteringsportalen **regel grupper**.
2. På den **regelgrupper** klickar du på **standard regelgruppen för Azure Web App**.
3. På den **redigera regelgruppen** klickar du på **generera**.
4. På den **generera regler: standard regelgruppen för Azure Web App** , se till att Windows Live ID är markerad och klickar sedan på **generera**.    
5. På den **redigera regelgruppen** klickar du på **spara**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Överföra ett certifikat till ACS-namnområde
I den här uppgiften som du överför en. PFX-certifikat som ska användas för att logga förfrågningar om säkerhetstoken skapas av ACS-namnområdet.

1. Klicka på huvudsidan i ACS-hanteringsportalen **certifikat och nycklar**.
2. På den **certifikat och nycklar** klickar du på **Lägg till** ovan **tokensignering**.
3. På den **Lägg till-certifikatet för tokensignering eller nyckel** sidan:
   1. I den **används för** klickar du på **förlitande partsprogram** och välj **Azure Web App** (som du skapade tidigare som namnet på tillämpningsprogrammet förlitande part).
   2. I den **typen** väljer **X.509-certifikat**.
   3. I den **certifikat** avsnittet, klicka på Bläddra och navigera till filen X.509-certifikat som du vill använda. Det här är en. PFX-filen. Markera filen och klicka på **öppna**, och ange lösenord för certifikatet i den **lösenord** textruta. Observera att du kan använda en egen-signed-certifikat för testning. För att skapa ett självsignerat certifikat, använder den **ny** knappen i den **ACS Filter biblioteket** dialogrutan (beskrivs senare) eller Använd den **encutil.exe** utility från [projekt webbplats] [ project website] av Azure startpaket för Java.
   4. Se till att **gör primär** är markerad. Din **Lägg till-certifikatet för tokensignering eller nyckel** sidan bör likna följande.
       ![Lägg till certifikat för tokensignering][add_token_signing_cert]
   5. Klicka på **spara** att spara inställningarna och stänga den **Lägg till-certifikatet för tokensignering eller nyckel** sidan.

Sedan granska informationen på sidan Programintegrationstyp och kopiera den URI som du behöver konfigurera webbprogrammet Java att använda ACS.

## <a name="review-the-application-integration-page"></a>Kontrollera sidan Programintegrationstyp
Du hittar all information och koden behövs för att konfigurera Java-webbprogram (RP-tjänstprogrammet) för ACS på sidan Programintegrationstyp i ACS-hanteringsportalen. Du behöver den här informationen när du konfigurerar ditt webbprogram för Java för federerad autentisering.

1. Klicka på hanteringsportalen ACS **programintegrationstyp**.  
2. I den **Programintegrationstyp** klickar du på **inloggningssidor**.
3. I den **inloggningen sidan Integration** klickar du på **Azure Web App**.

I den **inloggningen sidan integrering: Azure Web App** sidan den URL som visas i **alternativ 1: länk till en ACS-värdbaserad inloggningssidan** kommer att användas i Java-webbapp. Du behöver det här värdet när du lägger till Azure Access Control tjänstfilter biblioteket till Java-programmet.

## <a name="create-a-java-web-application"></a>Skapa ett Java-webbprogram
1. I Eclipse menyn klickar du på **filen**, klickar du på **ny**, och klicka sedan på **dynamiskt webbprojekt**. (Om du inte ser **dynamiskt webbprojekt** visas som tillgängliga projekt när du klickar på **filen**, **ny**, gör du följande: Klicka på **filen**, klickar du på **ny**, klickar du på **projekt**, expandera **Web**, klickar du på **dynamiskt webbprojekt**, och klicka på  **Nästa**.) Namnge projektet för den här kursen är **MyACSHelloWorld**. (Se till att du använder det här namnet, efterföljande steg i den här självstudiekursen räknar WAR-filen namnet MyACSHelloWorld). Skärmen visas som liknar följande:
   
    ![Skapa ett Hello World-projekt för ACS exampple][create_acs_hello_world]
   
    Klicka på **Slutför**.
2. I Eclipses Projektutforskaren vy Expandera **MyACSHelloWorld**. Högerklicka på **Webbinnehåll**, klicka på **Ny** och sedan på **JSP-fil**.
3. I den **ny JSP-fil** dialogrutan, namnge filen **index.jsp**. Behåll den överordnade mappen som MyACSHelloWorld/webbinnehåll som visas i följande:
   
    ![Lägg till en JSP-fil till ACS-exempel][add_jsp_file_acs]
   
    Klicka på **Nästa**.
4. I den **Välj JSP-mall** väljer **ny JSP-fil (html)** och på **Slutför**.
5. När index.jsp-filen öppnas i Eclipse, lägga till i text som ska visas **Hello ACS World!** i det befintliga `<body>`-elementet när index.jsp-filen öppnas i Eclipse. Uppdaterade `<body>` innehåll ska visas som följande:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Spara index.jsp.

## <a name="add-the-acs-filter-library-to-your-application"></a>Lägga till Filter för ACS-bibliotek i ditt program
1. Högerklicka i Eclipses Projektutforskaren **MyACSHelloWorld**, klickar du på **byggsökväg**, och klicka sedan på **konfigurera byggsökväg**.
2. I den **Java byggsökväg** dialogrutan klickar du på den **bibliotek** fliken.
3. Klicka på **lägga till bibliotek**.
4. Klicka på **Azure Access Control tjänstfilter (genom att öppna teknisk för MS)** och klicka sedan på **nästa**. Den **Azure Access Control tjänstfilter** dialogrutan visas.  (Den **plats** fält kan ha en annan sökväg, beroende på där du installerade Eclipse och versionsnumret kan vara olika beroende på programuppdateringar.)
   
    ![Lägga till Filter för ACS-bibliotek][add_acs_filter_lib]
5. Med en webbläsare öppnas till den **inloggningen sidan Integration** sidan i hanteringsportalen, Kopiera URL som anges i den **alternativ 1: länk till en ACS-värdbaserad inloggningssidan** fältet och klistrar in det i den **ACS Autentiseringsslutpunkten** i dialogrutan Eclipse.
6. Med en webbläsare öppnas till den **redigera förlitande partsprogram** sidan i hanteringsportalen, Kopiera URL som anges i den **sfär** fältet och klistrar in det i den **förlitande part sfär**i dialogrutan Eclipse.
7. I den **säkerhet** avsnitt i Eclipse dialogrutan om du vill använda ett befintligt certifikat klickar du på **Bläddra**, navigera till de certifikat som du vill använda markerar du den och klicka på **öppna**. Om du vill skapa ett nytt certifikat klickar du på **ny** att visa den **nytt certifikat** dialogrutan Ange lösenord, namnet på den cer-fil och namnet på PFX-filen för det nya certifikatet.
8. Kontrollera **bädda in certifikatet i WAR-filen**. Bädda in certifikatet i det här sättet inkluderar den i distributionen utan att du manuellt lägga till det som en komponent. (Om i stället måste du lagra certifikatet externt från WAR-filen, kan du lägga till certifikatet som en roll och avmarkera **bädda in certifikatet i WAR-filen**.)
9. [Valfritt] Behåll **kräver HTTPS-anslutningar** markerad. Om du ställer in det här alternativet om behöver du åtkomst till ditt program med hjälp av HTTPS-protokollet. Avmarkera det här alternativet om du inte vill att kräva HTTPS-anslutningar.
10. För en distribution till beräkningsemulatorn, din **Azure ACS Filter** ser ut ungefär så här inställningarna.
    
    ![Azure ACS filterinställningar för en distribution till beräkningsemulatorn][add_acs_filter_lib_emulator]
11. Klicka på **Slutför**.
12. Klicka på **Ja** när visas med en dialogruta anger att en web.xml-fil kommer att skapas.
13. Klicka på **OK** att stänga den **Java byggsökväg** dialogrutan.

## <a name="deploy-to-the-compute-emulator"></a>Distribuera till beräkningsemulatorn
1. Högerklicka i Eclipses Projektutforskaren **MyACSHelloWorld**, klickar du på **Azure**, och klicka sedan på **paketet för Azure**.
2. För **projektnamn**, typen **MyAzureACSProject** och på **nästa**.
3. Välj en JDK och programservrar. (Dessa steg beskrivs i detalj i den [skapar en Hello World program för Azure i Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) kursen).
4. Klicka på **Slutför**.
5. Klicka på den **körs i Azure-emulatorn** knappen.
6. När Java-webbapp startar i beräkningsemulatorn, Stäng alla instanser av webbläsaren (så att alla aktuella webbläsarsessioner inte stör ACS inloggningen testet).
7. Kör ditt program genom att öppna <http://localhost:8080/MyACSHelloWorld/> i webbläsaren (eller <https://localhost:8080/MyACSHelloWorld/> när du har markerat **kräver HTTPS-anslutningar**). Du ska ange ett Windows Live ID-inloggning och du bör vidtas för att returnera Webbadressen som angetts för tillämpningsprogrammet förlitande part.
8. När du är klar visar-programmet, klickar du på den **återställa Azure-emulatorn** knappen.

## <a name="deploy-to-azure"></a>Distribuera till Azure
Om du vill distribuera till Azure måste du ändra realm för den förlitande parten och retur-URL för ACS-namnområdet.

1. I Azure-hanteringsportalen, i den **redigera förlitande partsprogram** sidan genom att ändra **sfär** ska platsens distribuerade URL. Ersätt **exempel** med DNS-namn du angav för din distribution.
   
    ![Förlitande part sfär för användning i produktion][relying_party_realm_production]
2. Ändra **Retur-URL** till URL: en för programmet. Ersätt **exempel** med DNS-namn du angav för din distribution.
   
    ![Förlitande part Retur-URL för användning i produktion][relying_party_return_url_production]
3. Klicka på **spara** att spara din uppdaterade svarande part sfär och returnera URL ändringar.
4. Behåll den **inloggningen sidan Integration** sidan öppen i webbläsaren, måste du kopiera från den inom kort.
5. Högerklicka i Eclipses Projektutforskaren **MyACSHelloWorld**, klickar du på **byggsökväg**, och klicka sedan på **konfigurera byggsökväg**.
6. Klicka på den **bibliotek** klickar du på **Azure Access Control tjänstfilter**, och klicka sedan på **redigera**.
7. Med en webbläsare öppnas till den **inloggningen sidan Integration** sidan i hanteringsportalen, Kopiera URL som anges i den **alternativ 1: länk till en ACS-värdbaserad inloggningssidan** fältet och klistrar in det i den **ACS Autentiseringsslutpunkten** i dialogrutan Eclipse.
8. Med en webbläsare öppnas till den **redigera förlitande partsprogram** sidan i hanteringsportalen, Kopiera URL som anges i den **sfär** fältet och klistrar in det i den **förlitande part sfär**i dialogrutan Eclipse.
9. I den **säkerhet** avsnitt i Eclipse dialogrutan om du vill använda ett befintligt certifikat klickar du på **Bläddra**, navigera till de certifikat som du vill använda markerar du den och klicka på **öppna**. Om du vill skapa ett nytt certifikat klickar du på **ny** att visa den **nytt certifikat** dialogrutan Ange lösenord, namnet på den cer-fil och namnet på PFX-filen för det nya certifikatet.
10. Behåll **bädda in certifikatet i WAR-filen** kontrolleras, förutsatt att du vill bädda in certifikatet i WAR-filen.
11. [Valfritt] Behåll **kräver HTTPS-anslutningar** markerad. Om du ställer in det här alternativet om behöver du åtkomst till ditt program med hjälp av HTTPS-protokollet. Avmarkera det här alternativet om du inte vill att kräva HTTPS-anslutningar.
12. Azure ACS filterinställningar ser ut ungefär så här för en distribution till Azure.
    
    ![Azure ACS filterinställningar för en Produktionsdistribution][add_acs_filter_lib_production]
13. Klicka på **Slutför** att stänga den **redigera biblioteket** dialogrutan.
14. Klicka på **OK** att stänga den **egenskaper för MyACSHelloWorld** dialogrutan.
15. I Eclipse klickar du på den **publicera till Azure-molnet** knappen. Svara på frågorna liknande som görs den **att distribuera programmet till Azure** avsnitt i den [skapar en Hello World program för Azure i Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) avsnittet. 

När ditt webbprogram har distribuerats, Stäng eventuella öppna webbläsarsessioner och köra ditt webbprogram bör du uppmanas att logga in med autentiseringsuppgifterna för Windows Live ID, följt av som skickas till den returnera-URL för förlitande part programmet.

När du är klar med tillämpningsprogrammet ACS Hello World Kom ihåg att ta bort distributionen (du kan lära dig hur du tar bort en distribution i den [skapar en Hello World program för Azure i Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) avsnittet).

## <a name="next_steps"></a>Nästa steg
En undersökning av den Security Assertion Markup Language (SAML) returnerades av ACS i tillämpningsprogrammet finns [hur du visar SAML returnerades av Azure Access Control Service][How to view SAML returned by the Azure Access Control Service]. Utforska att ytterligare funktioner för Gransknings-och insamlingstjänstens och om du vill experimentera med mer avancerade scenarier, se [Access Control Service 2.0][Access Control Service 2.0].

Dessutom det här exemplet används den **bädda in certifikatet i WAR-filen** alternativet. Det här alternativet gör det enkelt att distribuera certifikatet. Du kan använda följande metod om du i stället vill hålla ditt signeringscertifikat separat från WAR-filen:

1. I den **säkerhet** avsnitt i den **Azure Access Control tjänstfilter** dialogrutan, ange **${env. JAVA_HOME}/MyCert.cer** och avmarkerar **bädda in certifikatet i WAR-filen**. (Justera mycert.cer din certifikatfilnamnet är olika.) Klicka på **Slutför** att stänga dialogrutan.
2. Kopiera certifikatet som en komponent i distributionen: Expandera i Eclipses Projektutforskaren **MyAzureACSProject**, högerklicka på **WorkerRole1**, klickar du på **egenskaper**, Expandera **Azure rollen**, och klicka på **komponenter**.
3. Klicka på **Lägg till**.
4. I den **Lägg till komponent** dialogrutan:
   
   1. I den **importera** avsnitt:
      1. Använd den **filen** för att navigera till det certifikat som du vill använda. 
      2. För **metoden**väljer **kopiera**.
   2. För **namnet som**, klicka på rutan och acceptera standardnamnet.
   3. I den **distribuera** avsnitt:
      1. För **metoden**väljer **kopiera**.
      2. För **till katalogen**, typen **JAVA_HOME %**.
   4. Din **Lägg till komponent** dialogrutan bör likna följande.
      
       ![Lägg till certifikat-komponent][add_cert_component]
   5. Klicka på **OK**.

Certifikatet skulle nu ingår i distributionen. Observera att oavsett om du bädda in certifikatet i WAR-filen eller lägga till den som en komponent i din distribution, behöver du överför certifikatet till namnområdet som beskrivs i den [överföra ett certifikat till ACS-namnområdet] [ Upload a certificate to your ACS namespace] avsnitt.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

