---
title: Testkörning för Azure Resource Manager | Azure Marketplace
description: Skapa en Marketplace-testenhet med Azure Resource Manager
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275942"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager Test Drive

Den här artikeln är för utgivare som har sitt erbjudande på Azure Marketplace, eller som finns på AppSource men vill bygga sin testenhet med endast Azure-resurser.

En Azure Resource Manager-mall (Resource Manager) är en kodad behållare med Azure-resurser som du utformar för att bäst representera din lösning. Om du inte känner till vad en Resource Manager-mall är läser du upp [hur du förstår Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) och skapar Resource [Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) för att se till att du vet hur du skapar och testar dina egna mallar.

Vad Test Drive gör är att den tar den angivna Resource Manager-mallen och gör en distribution av alla resurser som krävs från den Resource Manager-mallen till en resursgrupp.

Om du väljer att skapa en Testlust för Azure Resource Manager är kraven att du ska:

- Skapa, testa och ladda sedan upp mallen Test Drive Resource Manager.
- Konfigurera alla nödvändiga metadata och inställningar för att aktivera testenheten.
- Publicera erbjudandet igen med Test Drive aktiverat.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Så här skapar du en Test drive för Azure Resource Manager

Här är processen för att skapa en Azure Resource Manager Test Drive:

1. Utforma vad du vill att dina kunder ska göra i ett flödesdiagram.
1. Definiera vilka upplevelser du vill att dina kunder ska bygga.
1. Baserat på ovanstående definitioner bestämmer du vilka delar och resurser som behövs för att kunderna ska kunna utföra en sådan upplevelse: till exempel D365-instans eller en webbplats med en databas.
1. Bygg designen lokalt och testa upplevelsen.
1. Paketera upplevelsen i en ARM-malldistribution och därifrån:
    1. Definiera vilka delar av resurserna som är indataparametrar.
    1. Vilka variabler är;
    1. Vilka resultat ges till kundupplevelsen.
1. Publicera, testa och gå live.

Den viktigaste delen med att skapa en Azure Resource Manager Test Drive är att definiera vilka scenarion du vill att dina kunder ska uppleva. Är du en brandvägg produkt och du vill demo hur väl du hanterar skript injektion attacker? Är du en lagringsprodukt och du vill demo hur snabbt och enkelt din lösning komprimerar filer?

Se till att spendera tillräckligt mycket tid på att utvärdera vilka som är de bästa sätten att visa upp din produkt. Specifikt runt alla nödvändiga resurser som du behöver, eftersom det gör det tillräckligt enklare att paketera Resource Manager-mallen.

För att fortsätta med vårt brandväggsexempel kan arkitekturen vara att du behöver en offentlig IP-URL för din tjänst och en annan offentlig IP-URL för webbplatsen som brandväggen skyddar. Varje IP distribueras på en virtuell dator och ansluts tillsammans med en nätverkssäkerhetsgrupp + nätverksgränssnitt.

När du har utformat önskat paket med resurser, nu kommer att skriva och bygga testenhet Resource Manager mall.

## <a name="writing-test-drive-resource-manager-templates"></a>Skriva mallar för Resurshanteraren för testlust

Test Drive kör distributioner i ett helt automatiserat läge, och på grund av detta har Test Drive-mallar vissa begränsningar som beskrivs nedan.

### <a name="parameters"></a>Parametrar

De flesta mallar har en uppsättning parametrar. Parametrar definierar resursnamn, resursstorlekar (till exempel typer av lagringskonton eller storlekar på virtuella datorer), användarnamn och lösenord, DNS-namn och så vidare. När du distribuerar lösningar med Azure-portalen kan du fylla i alla dessa parametrar manuellt, välja tillgängliga DNS-namn eller lagringskontonamn och så vidare.

![Lista över parametrar i en Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Test drive fungerar dock i ett helautomatiskt läge, utan mänsklig interaktion, så den stöder bara en begränsad uppsättning parameterkategorier. Om en parameter i mallen Test Drive Resource Manager inte hör till någon av de kategorier som stöds måste du **ersätta den här parametern med ett variabel- eller konstantvärde.**

Du kan använda valfritt giltigt namn för dina parametrar, Test Drive känner igen parameterkategorin med hjälp av metadatatypsvärde. Du **måste ange metadatatyp för varje mallparameter,** annars kommer mallen inte att skicka validering:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

Det är också viktigt att notera att **alla parametrar är valfria,** så om du inte\'vill använda någon, behöver\'du inte.

### <a name="accepted-parameter-metadata-types"></a>Metadatatyper för godkända parametrar

| Typ av metadata   | Parametertyp  | Beskrivning     | Exempelvärde    |
|---|---|---|---|
| **baseuri (avbasuri)**     | sträng          | Bas-URI för ditt distributionspaket| \//\<https:\.. \>.blob.core.windows.net/\<\..\> |
| **Användarnamn**    | sträng          | Nytt slumpmässigt användarnamn.| admin68876      |
| **lösenord**    | säker sträng    | Nytt slumpmässigt lösenord | Lp!ACS\^2kh     |
| **sessions-ID**   | sträng          | Unikt testenhetssessions-ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri (avbasuri)

Test Drive initierar den här parametern med en **Base Uri** i distributionspaketet, så att du kan använda den här parametern för att konstruera Uri för alla filer som ingår i paketet.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

I mallen kan du använda den här parametern för att skapa en Uri av valfri fil från testenhetens distributionspaket. Exemplet nedan visar hur du skapar en Uri av den länkade mallen:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>användarnamn

Test Drive initierar den här parametern med ett nytt slumpmässigt användarnamn:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Exempelvärde:

    admin68876

Du kan använda antingen slumpmässiga eller konstanta användarnamn för din lösning.

#### <a name="password"></a>password

Test Drive initierar den här parametern med ett nytt slumpmässigt lösenord:

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Exempelvärde:

    Lp!ACS^2kh

Du kan använda antingen slumpmässiga eller konstanta lösenord för din lösning.

#### <a name="session-id"></a>sessions-ID

Test Drive initierar den här parametern med ett unikt GUID som representerar testkörningssessions-ID:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Exempelvärde:

    b8c8693e-5673-449c-badd-257a405a6dee

Du kan använda den här parametern för att unikt identifiera testkörningssessionen, om det behövs.

### <a name="unique-names"></a>Unika namn

Vissa Azure-resurser, till exempel lagringskonton eller DNS-namn, kräver globalt unika namn.

Det innebär att varje gång Test Drive distribuerar Resource Manager-mallen skapas en **ny resursgrupp med ett unikt namn** för alla resurser.\' Därför krävs det att använda den [unika funktionen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) som sammanfogats med variabelnamnen på resursgrupp-ID:er för att generera slumpmässiga unika värden:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Se\'till att du sammanfogar parameter-/variabelsträngarna (contosovm)\'\'med en unik\'strängutdata (resourceGroup().id ), eftersom detta garanterar unikheten och tillförlitligheten för varje variabel.

De flesta resursnamn kan till exempel inte börja med en siffra, men unik strängfunktion kan returnera en sträng som börjar med en siffra. Så om du använder rå unik strängutdata misslyckas distributionerna. 

Du hittar ytterligare information om resursnamnregler och begränsningar i [den här artikeln](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Distributionsplats

Du kan göra testkörning tillgänglig i olika Azure-regioner. Tanken är att tillåta en användare att välja den närmaste regionen, för att ge med odjuret användarupplevelse.

När Test Drive skapar en instans av labbet skapas alltid en resursgrupp i den region som valts av en användare och kör sedan distributionsmallen i den här gruppkontexten. Mallen bör därför välja distributionsplatsen från resursgruppen:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Och använd sedan den här platsen för varje resurs för en viss Lab-instans:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Du måste se till att din prenumeration tillåts distribuera alla resurser som du vill distribuera i var och en av de regioner som du väljer. Du måste också se till att avbildningarna för virtuella datorer är tillgängliga i alla regioner som du ska aktivera, annars fungerar inte distributionsmallen för vissa regioner.

### <a name="outputs"></a>Utdata

Normalt med Resource Manager-mallar kan du distribuera utan att producera några utdata. Detta beror på att du känner till alla värden som du använder för att fylla i mallparametrar och du kan alltid manuellt granska egenskaper för alla resurser.

För Test Drive Resource Manager-mallar är det\'dock viktigt att återgå till Test Drive all information, som krävs för att få tillgång till labbet (URL:er för webbplatsen, värdnamn för virtuella datorer, användarnamn och lösenord). Kontrollera att alla utdatanamn är läsbara eftersom dessa variabler presenteras för kunden.

Det finns inga begränsningar relaterade till mallutdata. Kom bara ihåg att Test Drive konverterar alla utdatavärden till **strängar,** så om du skickar ett objekt till utdata ser en användare JSON-strängen.

Exempel:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Begränsningar för prenumeration

En sak du bör ta hänsyn till är abonnemang och servicegränser. Om du till exempel vill distribuera upp till tio virtuella datorer med fyra kärnor måste du se till att prenumerationen du använder för ditt labb gör att du kan använda 40 kärnor.

Du hittar mer information om Azure-prenumerations- och tjänstgränser i [den här artikeln](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Eftersom flera testenheter kan tas samtidigt kontrollerar du att \# din prenumeration kan hantera kärnorna multiplicerat med det totala antalet samtidiga testenheter som kan tas.

### <a name="what-to-upload"></a>Vad du ska ladda upp

Test Drive Resource Manager-mallen överförs som en zip-fil, som kan innehålla olika distributionsartefakter, men måste ha en fil med namnet **main-template.json**. Den här filen är Distributionsmall för Azure Resource Manager och Test Drive använder den för att instansiera ett labb.

Om du har ytterligare resurser utöver den här filen kan du referera till den som en extern resurs i mallen, eller så kan du inkludera resursen i zip-filen.

Under publiceringscertifieringen packar Test Drive upp distributionspaketet och placerar innehållet i en intern testenhetsblobbbehållare. Behållarstrukturen återspeglar distributionspaketets struktur:

| package.zip (på/till)                       | Test Drive blob-behållare         |
|---|---|
| huvudmall.json                | \//\<https:\... \>.blob.core.windows.net/\<\... \>/huvudmall.json  |
| mallar/solution.json           | \//\<https:\... \>.blob.core.windows.net/\<\... \>/templates/solution.json |
| skript/warmup.ps1                | \//\<https:\... \>.blob.core.windows.net/\<\... \>/scripts/warmup.ps1  |


Vi kallar en Uri av denna blob container Base Uri. Varje revidering av ditt labb har sin egen blob-behållare, och därför har varje revidering av ditt labb sin egen Base Uri. Test Drive kan skicka en Base Uri av ditt uppackade distributionspaket i mallen via mallparametrar.

## <a name="transforming-template-examples-for-test-drive"></a>Omvandla mallexempel för testenhet

Processen från att omvandla en arkitektur med resurser till en Test Drive Resource Manager-mall kan vara skrämmande. För att underlätta processen har\'vi gjort exempel på hur du bäst [kan omvandla aktuella distributionsmallar här](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Så här publicerar du en provkörning

Nu när du har byggt testenheten går det här avsnittet igenom vart och ett av de fält som krävs för att du ska kunna publicera testenheten.

![Aktivera provkörning i användargränssnittet](./media/azure-resource-manager-test-drive/howtopub1.png)

Det första och viktigaste fältet är att växla om du vill att Test Drive ska aktiveras för ditt erbjudande eller inte. När du väljer **Ja** visas resten av formuläret med alla obligatoriska fält som du kan fylla i. När du väljer **Nej** inaktiveras formuläret och om du publicerar om med testenheten inaktiverad tas testenheten bort från produktionen.

Om det finns några testenheter som används aktivt av användare fortsätter dessa testenheter att köras tills sessionen går ut.

### <a name="details"></a>Information

Nästa avsnitt att fylla i är information om ditt Test Drive-erbjudande.

![Detaljerad information om testkörning](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beskrivning -** *Krävs* Det är här du skriver huvudbeskrivningen om vad som finns på din provkörning. Kunden kommer hit för att läsa vilka scenarier din testkörning kommer att täcka om din produkt. 

**Användarhandbok -** *Krävs* Detta är den djupgående genomgång av din Test Drive-upplevelse. Kunden kommer att öppna detta och kan gå igenom exakt vad du vill att de ska göra under hela sin provkörning. Det är viktigt att detta innehåll är lätt att förstå och följa! (Måste vara en PDF-fil)

**Test Drive Demo Video -** *Rekommenderas* i likhet med bruksanvisningen, är det bäst att inkludera en video tutorial av din Test Drive erfarenhet. Kunden kommer att titta på detta före eller under sin provkörning och kan gå igenom exakt vad du vill att de ska göra under hela sin provkörning. Det är viktigt att detta innehåll är lätt att förstå och följa!

- **Namn** - Titel på din video
- **Länk** - Måste vara en inbäddad webbadress från ditt rör eller video. Exempel på hur du får den inbäddade webbadressen finns nedan:
- **Miniatyr** - Måste vara en högkvalitativ bild (533x324) pixlar. Det rekommenderas att ta en skärmdump av någon del av din Test Drive-upplevelse här.

Nedan visas hur dessa fält visas för din kund under deras provkörningsupplevelse.

![Plats för testkörningsfält i Marketplace-erbjudandet](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Teknisk konfiguration

Nästa avsnitt som ska fyllas i är där du laddar upp mallen Test Drive Resource Manager och definierar hur specifikt dina Test Drive-instanser fungerar.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instanser –** *Obligatoriskt* Det är här du konfigurerar hur många instanser du vill ha, i vilka eller flera regioner och hur snabbt dina kunder kan få provkörningen.

- **Instanser** - Välj regioner är där du väljer var testenhetsresurshanterarens mall distribueras i. Det rekommenderas att bara välja en region där du mest förväntar dig att dina kunder ska vara belägna på.
- **Hot** - Antal Test Drive-instanser som redan har distribuerats och väntar på åtkomst per vald region. Kunder kan omedelbart komma åt dessa testenheter i stället för att behöva vänta på en distribution. Avvägningen är att dessa instanser alltid körs på din Azure-prenumeration, så att de medför en större drifttidskostnad. Det rekommenderas starkt att ha **minst en Hot-instans,** eftersom de flesta av dina kunder inte vill vänta på att fullständiga distributioner ska slutföras och det finns ett avbrott i kundanvändningen.
- **Varm** - Antal Test Drive-instanser per region som har distribuerats och sedan har den virtuella datorn stoppats och lagrats i Azure-lagring. Väntetiden för varma instanser är långsammare än Heta instanser, men kostnaden för lagring är också billigare.
- **Kallt** - Antal Test Drive-instanser per region som eventuellt kan distribueras. Kalla instanser kräver att hela Test Drive Resource Manager-mallen går igenom en distribution när en kund begär provkörningen, så den är långsammare än heta eller varma instanser. Kompromissen är dock att du bara behöver betala under testkörningens varaktighet.

För närvarande beräknas det totala antalet potentiella samtidiga testenheter som du ska göra tillgängliga och kontrollera att kvotgränsen för prenumerationen kan hantera det samtidiga beloppet:

**(Antal markerade regioner x Heta instanser) + (Antal markerade regioner x Varma förekomster) + (Antal markerade regioner x Kalla förekomster)**

**Provkörningslängd (timmar) -** *Önskad* varaktighet för hur länge \# provkörningen ska vara aktiv, i timmar. Provkörningen avslutas automatiskt när den här tidsperioden är.

**Mallen Test Drive Resource Manager -** *Obligatorisk* uppladdning av Resource Manager-mallen här. Det här är filen som du har skapat i föregående avsnitt ovan. Namnge huvudmallfilen: "main-template.json" och se till att resurshanteraren-mallen innehåller utdataparametrar för nyckelvariabler som behövs. (Måste vara en ZIP-fil)

**Åtkomstinformation -** *Krävs* när en kund får sin provkörning presenteras åtkomstinformationen för dem. De här instruktionerna är avsedda att dela de användbara utdataparametrarna från mallen Test Drive Resource Manager. Om du vill inkludera utdataparametrar använder du dubbla kladdiga parenteser (till exempel **{{outputname}}**) och de infogas korrekt på platsen. (HTML-strängformatering rekommenderas här för att återge i fronten).

### <a name="test-drive-deployment-subscription-details"></a>Prenumerationsinformation för distribution av testkörning

Det sista avsnittet som ska fyllas i är att kunna distribuera testenheterna automatiskt genom att ansluta din Azure-prenumeration och Azure Active Directory (AD).

![Information om prenumerationsinformation för testkörningsdistribution](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-prenumerations-ID -** *Krävs* Detta ger åtkomst till Azure-tjänster och Azure-portalen. Prenumerationen är där resursanvändning rapporteras och tjänster faktureras. Om du inte redan har en **separat** Azure-prenumeration för testenheter gör du en. Du hittar Azure-prenumerations-ID:er genom att logga in på Azure-portalen och navigera till prenumerationerna på menyn till vänster. (Exempel: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-prenumerationer](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD Tenant ID -** *Krävs* Om du redan har ett klient-ID som redan finns tillgängligt kan du hitta det nedan i Properties -\> Directory ID.

![Azure Active Directory-egenskaper](./media/azure-resource-manager-test-drive/subdetails3.png)

Annars skapar du en ny klient i Azure Active Directory.

![Lista över Azure Active Directory-klienter](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definiera organisation, domän och land/region för Azure AD-klienten](./media/azure-resource-manager-test-drive/subdetails5.png)

![Bekräfta markeringen](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App ID -** *Krävs* Nästa steg är att skapa och registrera ett nytt program. Vi använder det här programmet för att utföra åtgärder på din Test Drive-instans.

1. Navigera till den nyligen skapade katalogen eller redan befintlig katalog och välj Azure Active Directory i filterfönstret.
2. Sök "App registreringar" och klicka på "Lägg till"
3. Ange ett programnamn.
4. Välj typ av som "Webbapp / API"
5. Ange något värde i Sign-on\'URL, kommer vi inte att använda det fältet.
6. Klicka på Skapa.
7. När programmet har skapats går\> du till Egenskaper - Ange programmet som flera innehavare och träffar Spara.

Klicka på Spara. Det sista steget är att ta tag i program-ID för den här registrerade appen och klistra in det i fältet Test Drive här.

![Information om Azure AD-program-ID](./media/azure-resource-manager-test-drive/subdetails7.png)

Med tanke på att vi använder programmet för att distribuera till prenumerationen måste vi lägga till programmet som en deltagare i prenumerationen. Instruktionerna för dessa är följande:

1. Navigera till bladet Prenumerationer och välj lämplig prenumeration som du endast använder för provkörningen.
1. Klicka på **Åtkomstkontroll (IAM)**.
1. Klicka på fliken **Rolltilldelningar.**  ![Lägga till ett nytt huvudnamn för åtkomstkontroll](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicka på **Lägg till rolltilldelning**.
1. Ange rollen som **deltagare**.
1. Skriv in namnet på Azure AD-programmet och välj programmet för att tilldela rollen.
    ![Lägga till behörigheterna](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicka på **Spara**.

**Azure AD App Key -** *Krävs* Det sista fältet är att generera en autentiseringsnyckel. Lägg till en nyckelbeskrivning under tangenter, ange att varaktigheten aldrig ska upphöra att gälla och välj sedan spara. Det är **viktigt** att undvika att ha en utgången nyckel, som kommer att bryta din provkörning i produktion. Kopiera det här värdet och klistra in det i fältet Test Drive.

![Visar nycklarna för Azure AD-programmet](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har fyllt i alla testkörningsfält går du igenom och **publicerar om** erbjudandet. När din provkörning har godkänts bör du testa kundupplevelsen i **förhandsversionen** av erbjudandet. Starta en testenhet i användargränssnittet och öppna sedan din Azure-prenumeration i Azure-portalen och kontrollera att dina testenheter distribueras korrekt.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Det är viktigt att notera att du inte tar bort några Test Drive-instanser eftersom de är etablerade för dina kunder, så testkörningstjänsten rensar automatiskt upp dessa resursgrupper när en kund är klar med den.

När du känner dig bekväm med ditt förhandserbjudande, nu är det dags att **gå live!** Det finns en slutlig granskningsprocess från Microsoft när erbjudandet har publicerats för att dubbelkolla hela slutet till slut-upplevelsen. Om erbjudandet av någon anledning avvisas skickar vi ett meddelande till den tekniska kontakten för ditt erbjudande som förklarar vad som behöver fixas.

Om du har fler frågor, letar efter felsökningsråd eller vill göra din testkörning mer framgångsrik, gå till [vanliga frågor, felsökning, & bästa praxis](./marketing-and-best-practices.md).
