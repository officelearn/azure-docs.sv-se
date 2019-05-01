---
title: Azure Resource Manager-provkörning | Azure Marketplace
description: Skapa en Marketplace-Test-enhet med Azure Resource Manager
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Patrick .Butler
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 7665050dfc9a561f42fec00c40d0a40dfa5cc183
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941574"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager Test Drive

Den här artikeln är utgivare som har sitt erbjudande på Azure Marketplace eller som står på AppSource men vill bygga sina Test Drive med endast Azure-resurser.

En mall för Azure Resource Manager (Resource Manager) är en inbyggd behållare för Azure-resurser att du utformar för bästa representerar din lösning. Om du är bekant med vilken en Resource Manager-mall är kan du läsa mer om [förstå Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) och [skapa Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) att kontrollera att du vet hur du skapar och testa egna mallar.

Vad Test Drive gör är att det tar den angivna Resource Manager-mallen och gör en distribution av alla resurser som krävs med Resource Manager-mallen i en resursgrupp.

Om du vill skapa en Azure Resource Manager Test Drive är kraven för dig att:

- Skapa, testa och överför sedan testa enheten Resource Manager-mallen.
- Konfigurera alla metadata som krävs och inställningar för att aktivera din provkörning.
- Publicera ditt erbjudande med Test Drive aktiverat.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Hur du skapar en Azure Resource Manager-provkörning

Här är en process för att skapa en Azure Resource Manager Test Drive:

1. Utforma vad du vill att dina kunder att göra ett diagram i flödet.
1. Definiera vilka upplevelser som kunderna att bygga.
1. Baserat på ovanstående definitioner kan avgöra vilka delar och resurser som krävs för kunder att utföra sådana upplevelse: till exempel D365 instans eller en webbplats med en-databas.
1. Skapa designen lokalt och testa upplevelsen.
1. Paketet upplevelse i en ARM-mall för distribution, och därifrån:
    1. Definiera vilka delar av resurserna som är indataparametrar;
    1. Vilka variabler är;
    1. Vilka utdata ges till kundupplevelsen.
1. Publicera, testa och publicera.

Den viktigaste delen om att skapa en Azure Resource Manager Test Drive är att definiera vilka scenarier som du vill att dina kunder att uppleva. Är du en brandväggsprodukt och du vill demonstrera hur väl du hantera skript-inmatningsattacker? Är du en produkt för lagring och du vill demonstrera hur snabbt och enkelt din lösning komprimerar filer?

Se till att använda en tillräcklig tidsperiod som utvärderar vilka är de bästa sätten att visa upp din produkt. Mer specifikt runt alla nödvändiga resurser behöver, som gör det paketering Resource Manager-mallen tillräckligt enklare.

Om du vill fortsätta med vårt exempel för brandväggen kanske arkitekturen du behöver en offentlig IP-URL för din tjänst och en annan offentlig IP-URL för webbplatsen som din brandvägg skyddar. Varje IP distribueras på en virtuell dator och ansluten tillsammans med en nätverkssäkerhetsgrupp + nätverksgränssnitt.

När du har utformat det önskade paketet av resurser, kommer nu skriva och bygga Test Drive Resource Manager-mallen.

## <a name="writing-test-drive-resource-manager-templates"></a>Skriva Test Drive Resource Manager-mallar

Test Drive kör distributioner i en helt automatiserad läge och på grund av detta, Test Drive-mallar har vissa begränsningar som beskrivs nedan.

### <a name="parameters"></a>Parametrar

De flesta mallarna har en uppsättning parametrar. Parametrar som definierar resursnamn, storlekar för resurser (till exempel typer av lagringskonton eller storlekar för virtuella datorer), användarnamn och lösenord, DNS-namn och så vidare. När du distribuerar lösningar med hjälp av Azure-portalen kan du manuellt fylla i alla dessa parametrar, Välj tillgängliga DNS-namn eller namnet på ett lagringskonto och så vidare.

![Listan över parametrar i en Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Test Drive fungerar dock med helt automatisk, utan mänsklig interaktion, så att den stöder endast en begränsad uppsättning kategorier för parametern. Om en parameter i Test Drive Resource Manager-mallen inte har någon av kategorierna som stöds, måste du **ersätta den här parametern med en variabel eller konstant värde.**

Du kan använda valfritt giltigt namn för din parametrar, Test Drive identifierar parametern kategori med hjälp av metadatatyp värde. Du **måste ange metadatatyp för varje mallparameter**, annars mallen kommer inte valideras:

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

Det är också viktigt att notera att **alla parametrar är valfria**, så om du inte\'t vill använda någon, du\'måste.

### <a name="accepted-parameter-metadata-types"></a>Metadata för godkända parametertyper

| Metadatatyp   | Parametertyp  | Beskrivning     | Exempelvärde    |
|---|---|---|---|
| **BaseUri**     | sträng          | Bas-URI för distributionspaket| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **användarnamn**    | sträng          | Nytt slumpmässigt användarnamn.| admin68876      |
| **Lösenord**    | säker sträng    | Ny slumpmässigt lösenord | LP! ACS\^2kh     |
| **Sessions-id**   | sträng          | Unikt Test Drive-sessions-ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>användarnamn

Test Drive initierar den här parametern med en **bas-Uri** för ditt distributionspaket, så du kan använda den här parametern för att konstruera Uri till en fil som ingår i paketet.

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

Du kan använda den här parametern i din mall för att konstruera en Uri till en fil från din Test Drive-distributionspaket. Exemplet nedan visar hur du skapar en länkad mall-Uri:

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

Du kan använda slumpmässiga eller konstant användarnamn för din lösning.

#### <a name="password"></a>lösenord

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

Du kan använda slumpmässiga eller konstant lösenord för din lösning.

#### <a name="session-id"></a>Sessions-ID

Test Drive initiera den här parametern med ett unikt GUID som representerar Test Drive sessions-ID:

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

Du kan använda den här parametern för att unikt identifiera Test Drive-sessionen, om det behövs.

### <a name="unique-names"></a>Unika namn

Vissa Azure-resurser, t.ex. storage-konton eller DNS-namn, kräver globalt unika namn.

Det innebär att varje gång Test Drive distribuerar Resource Manager-mallen, skapas en **ny resursgrupp med ett unikt namn** för alla dess\' resurser. Därför det krävs för att använda den [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) funktionen sammanfogat med din variabelnamn på resursgruppen ID att skapa slumpmässigt unika värden:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Kontrollera att du sammanfoga parametern/variabeln-strängar (\'contosovm\') med en unik sträng-utdata (\'resourceGroup () .id\'), eftersom detta garanterar unikhet och tillförlitligheten för varje variabel.

Till exempel de flesta resursnamn får inte börja med en siffra, men unik Strängfunktion kan returnera en sträng som börjar med en siffra. Så om du använder unika rådatasträng utdata, misslyckas distributionen. 

Du hittar mer information om resursen namngivningsregler och begränsningar i [i den här artikeln](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions).

### <a name="deployment-location"></a>Distributionsplats

Du kan göra du Test Drive tillgängliga i olika Azure-regioner. Tanken är att tillåta en användare att välja den närmaste regionen, att tillhandahålla beast-upplevelse.

När Test Drive skapar en instans av labbet, skapas alltid en resursgrupp i regionen välja av en användare och kör sedan mallen för distribution i den här gruppen kontexten. Mallen ska därför välja distributionsplatsen från resursgrupp:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Och sedan använda den här platsen för varje resurs för en specifik labbinstans:

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

Du måste se till att din prenumeration kan du distribuera alla resurser som du vill distribuera i var och en av de regioner som du väljer. Dessutom måste du se till att dina avbildningar av virtuella datorer är tillgängliga i alla regioner som du ska aktivera, annars fungerar inte din Distributionsmall för vissa regioner.

### <a name="outputs"></a>Utdata

Normalt kan du distribuera utan den producerar inga utdata med Resource Manager-mallar. Detta beror på att du vet vilka värden som du använder för att fylla i mallparametrarna och du kan alltid manuellt granska egenskaperna för en resurs.

För Test Drive Resource Manager-mallar dock den\'s som är viktigt att återgå till Test Drive all information, vilket krävs för att få en till labbet (URI: er för webbplatsen, virtuella värdnamn, användarnamn och lösenord). Kontrollera att alla utdatanamn är läsbar eftersom dessa variabler presenteras för kunden.

Det finns inga eventuella begränsningar som rör mallutdata. Men kom ihåg Test Drive konverterar alla utdata värden till **strängar**, så om du skickar ett objekt utdata för en användare kan se JSON sträng.

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

### <a name="subscription-limits"></a>Prenumerationsbegränsningar

En sak du bör ta hänsyn är prenumeration och tjänstbegränsningar. Om du vill distribuera virtuella datorer med upp till tio 4-kärnor, måste du kontrollera att den prenumeration som du använder för ditt labb kan du använda 40-kärnor.

Du hittar mer information om Azure-prenumeration och tjänstbegränsningar i [i den här artikeln](https://docs.microsoft.com/azure/azure-subscription-service-limits). Eftersom flera Test Drives kan utföras på samma gång, kontrollera att din prenumeration kan hantera den \# kärnor multiplicerat med det totala antalet samtidiga Provkörningar som kan utföras.

### <a name="what-to-upload"></a>Vad du kan ladda upp

Test Drive Resource Manager-mallen har laddats upp som en zip-fil som kan innehålla olika artefakter för distribution, men den måste ha en fil med namnet **main-template.json**. Den här filen är Azure Resource Manager-mall för distribution och Test Drive används för att skapa en instans av ett labb.

Om du har ytterligare resurser utöver den här filen kan du kan använda den som en extern resurs i mallen eller du kan lägga till resursen i zip-filen.

Under publishing-certifiering, Test Drive upp distributionspaket och placerar innehållet i en intern Test Drive-blob-behållare. Behållarstrukturen visar strukturen för distributionspaket:

| Package.zip                       | Test Drive blob-behållare         |
|---|---|
| main-template.json                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| Templates/Solution.JSON           | https:\//\<\.... \>.blob.core.windows.net/\<\.... \>/templates/solution.json |
| Scripts/warmup.ps1                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1  |


Vi kallar en Uri för den här blobbehållaren bas-Uri. Varje revision över ditt labb har sin egen blob-behållare och därför varje revision över ditt labb har sin egen bas-Uri. Test Drive kan skicka en bas-Uri för uppzippade distributionspaket i mallen via mallparametrarna.

## <a name="transforming-template-examples-for-test-drive"></a>Omvandla mallexempel för provkörning

Processen från att aktivera en arkitektur för resurser i en Test Drive Resource Manager-mallar kan kännas avskräckande. För att underlätta den här processen, vi\'stött gjort exempel på hur till bästa [omvandla aktuella distributionsmallar här](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Så här publicerar du en Test Drive

Nu när du har din provkörning som skapats beskriver i det här avsnittet var och en av de fält som krävs att publicera din provkörning.

![Aktivera Test Drive i användargränssnittet](./media/azure-resource-manager-test-drive/howtopub1.png)

Fältet första och viktigaste är att ange om du vill Test Drive aktiverats för ditt erbjudande eller inte. När du väljer **Ja,** resten av formuläret med alla de obligatoriska fälten visas för att fylla. När du väljer **Nej,** formuläret blir inaktiverad och om du publicerar med Test Drive inaktiverat din provkörning tas bort från produktionen.

Obs! Om det finns några tester enheter som aktivt används av användare, fortsätter dessa Provkörningar att köras tills sin session upphör att gälla.

### <a name="details"></a>Information

Nästa avsnitt för att fylla i är information om din provkörning erbjuder.

![Test Drive detaljerad information](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beskrivning –** *krävs* det här är där du skriver den huvudsakliga beskrivningen om vad som är på din provkörning. Kunden kommer här för att läsa vilka scenarier som kommer som täcker din provkörning om produkten. 

**Användaren manuellt –** *krävs* det här är detaljerad genomgång av din Test Drive-upplevelse. Kunden öppnas det och kan gå igenom exakt vad du vill ha dem att göra i hela deras Test Drive. Det är viktigt att det här innehållet är lätt att förstå och följ! (Måste vara en PDF-fil)

**Testa enheten demonstrationsvideon -** *rekommenderas* användarhandboken ett liknande sätt, är det bäst att inkludera en självstudievideo av din Test Drive-upplevelse. Kunden ska titta på det här tidigare eller under deras Test Drive och kan gå igenom exakt vad du vill ha dem att göra i hela deras Test Drive. Det är viktigt att det här innehållet är lätt att förstå och följ!

- **Namn på** -rubriken för videon
- **Länken** -måste vara en inbäddad URL från rör eller video. Exempel på hur du hämtar den inbäddade URL: en är nedan:
- **Miniatyr** -måste vara en avbildning av hög kvalitet (533 x 324) bildpunkter. Vi rekommenderar att ta en skärmbild av någon del av din Test Drive-upplevelse här.

Nedan visas hur fälten visas för din kund under deras Test Drive-upplevelse.

![Platsen för Test Drive-fält i Marketplace-erbjudande](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Teknisk konfiguration

Nästa avsnitt för att fylla i kan du ladda upp din Test Drive Resource Manager-mall och definiera hur specifikt Test Drive instanser arbete.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**-Instanser –** *krävs* det här är konfigurerar du hur många instanser du vill, i vilken region, och hur snabbt dina kunder kan få Test Drive.

- **Instanser** – The Välj regioner är där du kan välja där Test Drive Resource Manager-mallen har distribuerats i. Vi rekommenderar att bara välja en region där du mest förväntar dig att kunderna ska finns i.
- **Frekvent** -nummer Provköra instanser som redan är distribuerad och väntar på åtkomst per valda regionen. Kunder kan direkt komma åt den här Provkörningar i stället för att behöva vänta på en distribution. Nackdelen är att dessa instanser körs alltid på din Azure-prenumeration, så leder de en större upptid kostnad. Vi rekommenderar starkt att ha **minst en frekvent instans**, eftersom de flesta av dina kunder inte vill vänta tills fullständiga distributioner ska slutföras och så att det finns en Samlingsbibliotek i KUNDANVÄNDNING.
- **Varma** – antal Provköra instanser per region som har distribuerats och sedan den virtuella datorn har stoppats och lagras i Azure storage. Väntetid för varm instanser är långsammare än frekventa instanser, men den drifttid kostnaden för lagring är också billigare.
- **Kalla** -nummer Provköra instanser per region som eventuellt kan distribueras. Kalla instanser kräver hela Test Drive Resource Manager-mallen gå igenom en distribution vid tidpunkten för en kund begär provkörning, så det är långsammare än för frekvent åtkomst eller varma instanser. Nackdelen är dock att du endast behöver betala för varaktigheten för Test Drive.

Just nu beräknar du det totala antalet möjliga samtidiga Provkörningar du ska vara tillgängliga och kontrollera att din kvotgräns för din prenumeration kan hantera det samtidiga beloppet:

**(Antal regioner valt x frekvent instanser) + (tal för valt regioner x varma instanser) + (tal för valt regioner x kalla instanser)**

**Driv varaktighet (timmar) -** *krävs* varaktigheten för hur länge Test Drive förblir aktiv i \# timmar. Test Drive avslutas automatiskt efter denna tid har löpt ut.

**Test Drive Resource Manager-mall -** *krävs* ladda upp här Resource Manager-mallen. Det här är den fil som du skapade i föregående avsnitt ovan. Namnge huvudsakliga mallfilen: ”main-template.json” och se till att Resource Manager-mallen innehåller utdataparametrar för viktiga variabler som krävs. (Måste vara en .zip-fil)

**Komma åt Information -** *krävs* när en kund får sin Test Drive kan komma åt information visas för dem. Dessa instruktioner är avsedda att dela användbara Utdataparametrarna från Test Drive Resource Manager-mallen. För att inkludera utdataparametrar, använda dubbla klammerparenteser (till exempel **{{outputname}}**), och de kommer att infogas korrekt på plats. (HTML-strängformatering bör här renderas i klientdelen).

### <a name="test-drive-deployment-subscription-details"></a>Prenumerationsinformation för Test Drive-distribution

Det är den sista delen att fylla i för att kunna distribuera Test Drives automatiskt genom att ansluta dina Azure-prenumeration och Azure Active Directory (AD).

![Detaljer om provkörning distribution prenumeration](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure prenumerations-ID -** *krävs* detta ger åtkomst till Azure-tjänster och Azure-portalen. Prenumerationen är där Resursanvändning rapporteras och tjänster faktureras. Om du inte redan har en **separat** Azure-prenumeration för Test Drives, gå vidare och gör en. Du hittar ID: N för Azure-prenumeration genom att logga in på Azure-portalen och gå till prenumerationer på menyn till vänster. (Exempel: ”a83645ac-1234-5ab6-6789-1h234g764ghty”)

![Azure-prenumerationer](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-klient-ID -** *krävs* om du har ett klient-ID redan finns du hittar den nedan i egenskaperna -\> katalog-ID

![Azure Active Directory-egenskaper](./media/azure-resource-manager-test-drive/subdetails3.png)

Annars skapar du en ny klient i Azure Active Directory.

![Lista över Azure Active Directory-klienter](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definiera organisation, domän och land för Azure AD-klient](./media/azure-resource-manager-test-drive/subdetails5.png)

![Bekräfta valet](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App-ID -** *krävs* nästa steg är att skapa och registrera ett nytt program. Vi använder det här programmet för att utföra åtgärder på din Test Drive-instans.

1. Gå till den nyligen skapade katalogen eller redan befintliga directory och välj Azure Active directory i filterrutan.
2. Sök efter ”appregistreringar” och klicka på ”Lägg till”
3. Ange ett programnamn.
4. Välj typ av som ”webbapp / API”
5. Ange ett värde i inloggnings-URL, vi vann\'t använder fältet.
6. Klicka på Skapa.
7. När programmet har skapats går du till egenskaper -\> ange programmet som flera innehavare och tryck på Spara.

Klicka på Spara. Det sista steget är att hämta program-ID för den här registrerad app och klistra in den i fältet Test Drive här.

![Azure AD-program-ID-information](./media/azure-resource-manager-test-drive/subdetails7.png)

Får vi använder programmet för att distribuera till prenumerationen, behöver vi lägga till programmet som deltagare för prenumerationen. Anvisningarna för dessa är som nedan:

1. Gå till bladet prenumerationer och välj lämplig prenumeration som du använder för Test-enheten.
1. Klicka på **åtkomstkontroll (IAM)**.
1. Klicka på den **rolltilldelningar** fliken.  ![Lägg till ett nytt huvudnamn för åtkomstkontroll](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicka på **Lägg till rolltilldelning**.
1. Ange rollen som **deltagare**.
1. Skriv namnet på Azure AD-programmet och Välj program att tilldela rollen.
    ![Lägg till behörigheter](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicka på **Spara**.

**Azure AD App Key -** *krävs* sista fältet är att generera en autentiseringsnyckel. Under nycklar, Lägg till en beskrivning av nyckeln, ange hur lång tid att aldrig upphöra, sedan väljer Spara. Det är **viktiga** för att förhindra att ett utgånget nyckel, vilket bryter din provkörning i produktion. Kopiera detta värde och klistra in den i din Test Drive-obligatoriskt.

![Visar nycklar för Azure AD-programmet](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har alla Test Drive fälten ifyllda, gå igenom och **publicera** ditt erbjudande. När din provkörning har gått ut certifiering, bör du gå en stor utsträckning testa kundupplevelsen i den **förhandsversion** i ditt erbjudande. Starta en Test Drive i Användargränssnittet och öppna din Azure-prenumeration i Azure-portalen och kontrollera att din Provkörningar fullständigt distribueras på rätt sätt.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Det är viktigt att Observera att du inte tar bort alla Test Drive-instanser som de har etablerats för dina kunder så Test Drive-tjänsten kommer automatiskt att rensa de här resursgrupperna upp när en kund har slutförts med den.

När du känner dig bekväm med ditt erbjudande förhandsversion nu är det dags att **Driftsätt**! Det finns en slutlig granskningsprocess från Microsoft när erbjudandet har publicerats till dubbla Kontrollera hela slutpunkt till slutpunkt-upplevelsen. Om erbjudandet hämtar avvisades av någon anledning, skickar vi ett meddelande till den tekniska kontakten för ditt erbjudande som förklarar vad kommer att behöva hämta har åtgärdats.

Om du har fler frågor, söker felsökningstips eller vill göra din provkörning lyckas, går du till [vanliga frågor och svar, felsökning och bästa praxis](./marketing-and-best-practices.md).
