---
title: Azure Resource Manager testen het | Azure Marketplace
description: Bygg en Marketplace-testenhet med Azure Resource Manager
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275942"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager Test Drive

Den här artikeln är för utgivare som har sitt erbjudande på Azure Marketplace eller som är på AppSource, men som vill bygga sin testenhet med endast Azure-resurser.

En mall för Azure Resource Manager (Resource Manager) är en kodad behållare för Azure-resurser som du utformar till att bäst representera din lösning. Om du inte är bekant med vad en Resource Manager-mall är, kan du läsa mer i [Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) och [Redigera Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) för att se till att du vet hur du skapar och testar dina egna mallar.

Vilken test enhet är att den har den tillhandahållna Resource Manager-mallen och gör en distribution av alla resurser som krävs från den Resource Manager-mallen till en resurs grupp.

Om du väljer att bygga en Azure Resource Manager testen het, så är kraven så att du kan:

- Bygg, testa och ladda upp din Test Drive Resource Manager-mall.
- Konfigurera alla obligatoriska metadata och inställningar för att aktivera test enheten.
- Publicera om erbjudandet med test driven aktiverat.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Så här skapar du en Azure Resource Manager testen het

Här är processen för att skapa en Azure Resource Manager testen het:

1. Designa vad du vill att dina kunder ska göra i ett flödes diagram.
1. Definiera vilka upplevelser som kunderna ska bygga.
1. Utifrån definitionerna ovan bestämmer du vilka delar och resurser som krävs för att kunderna ska kunna utföra sådan upplevelse: till exempel D365-instans eller en webbplats med en databas.
1. Skapa designen lokalt och testa upplevelsen.
1. Paketera upplevelsen i en distribution av ARM-mallar och därifrån:
    1. Definiera vilka delar av resurserna som är indataparametrar;
    1. Vilka variabler är,
    1. Vilka utdata ges till kund upplevelsen.
1. Publicera, testa och gå live.

Den viktigaste delen med att skapa en Azure Resource Manager-testenhet är att definiera vilka scenarier du vill att kunderna ska uppleva. Är du en brand Väggs produkt och vill lära dig hur väl du hanterar skript inmatnings attacker? Är du en lagrings produkt och vill lära dig hur snabb och enkel din lösning komprimerar filer?

Se till att ägna en tillräckligt lång tids period att utvärdera vad som är det bästa sättet att Visa produkten. Mer specifikt kring alla nödvändiga resurser som du behöver, eftersom det gör det enklare att paketera Resource Manager-mallen.

Om du vill fortsätta med vårt brand Väggs exempel kan arkitekturen vara att du behöver en offentlig IP-adress för din tjänst och en annan offentlig IP-adress för den webbplats som brand väggen skyddar. Varje IP-adress distribueras på en virtuell dator och är ansluten tillsammans med en nätverks säkerhets grupp + nätverks gränssnitt.

När du har utformat det önskade paketet med resurser, kommer nu att bli att skriva och bygga i Resource Manager-mallen för test enheten.

## <a name="writing-test-drive-resource-manager-templates"></a>Skriver Test Drive Resource Manager-mallar

Test enheten kör distributioner i ett helt automatiserat läge, och därför har mallar för att testa enheter vissa begränsningar som beskrivs nedan.

### <a name="parameters"></a>Parametrar

De flesta mallar har en uppsättning parametrar. Parametrar definierar resurs namn, resurs storlekar (till exempel typer av lagrings konton eller storlekar på virtuella datorer), användar namn och lösen ord, DNS-namn och så vidare. När du distribuerar lösningar med hjälp av Azure Portal kan du fylla i alla dessa parametrar manuellt, välja tillgängliga DNS-namn eller lagrings konto namn och så vidare.

![Lista över parametrar i en Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Test enheten fungerar dock i ett helt automatiskt läge, utan mänsklig interaktion, så att den endast stöder en begränsad uppsättning parameter kategorier. Om en parameter i Resource Manager-mallen för test enheten inte finns i någon av de kategorier som stöds, måste du **ersätta den med en variabel eller ett konstant värde.**

Du kan använda ett giltigt namn för dina parametrar, en test enhet som identifierar parameter kategori med hjälp av metadata-typ värde. Du **måste ange metadata-typ för varje mallparameter**, annars kommer din mall inte att klara verifieringen:

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

Det är också viktigt att Observera att **alla parametrar är valfria**, så om du\'inte vill använda någon behöver du\'inte göra det.

### <a name="accepted-parameter-metadata-types"></a>Godkända typer av metadata för parameter

| Typ av metadata   | Parameter typ  | Beskrivning     | Exempel värde    |
|---|---|---|---|
| **BaseUri**     | sträng          | Distributions paketets bas-URI| https:\//\<.\. \>. blob.Core.Windows.net/\<\..\> |
| **användar**    | sträng          | Nytt slumpmässigt användar namn.| admin68876      |
| **lösenord**    | säker sträng    | Nytt slumpmässigt lösen ord | LP! ACS\^-2kh     |
| **sessions-ID**   | sträng          | Unikt ID för test driven session-ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>BaseUri

Test enheten initierar den här parametern med en **bas-URI** för distributions paketet, så du kan använda den här parametern för att skapa en URI för en fil som ingår i paketet.

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

Inuti mallen kan du använda den här parametern för att skapa en URI för en fil från distributions paketet för test enheten. Exemplet nedan visar hur du skapar en URI för den länkade mallen:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>användarnamn

Test enheten initierar den här parametern med ett nytt slumpmässigt användar namn:

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

Exempel värde:

    admin68876

Du kan använda antingen slumpmässiga eller konstanta användar namn för din lösning.

#### <a name="password"></a>password

Test enheten initierar den här parametern med ett nytt slumpmässigt lösen ord:

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

Exempel värde:

    Lp!ACS^2kh

Du kan använda antingen slumpmässiga eller fasta lösen ord för din lösning.

#### <a name="session-id"></a>sessions-ID

Testa att initiera den här parametern med ett unikt GUID som representerar Test Drive sessions-ID:

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

Exempel värde:

    b8c8693e-5673-449c-badd-257a405a6dee

Du kan använda den här parametern för att unikt identifiera testen hets sessionen, om det behövs.

### <a name="unique-names"></a>Unika namn

Vissa Azure-resurser, t. ex. lagrings konton eller DNS-namn, kräver globalt unika namn.

Det innebär att varje gång test enheten distribuerar Resource Manager-mallen skapas en **Ny resurs grupp med ett unikt namn** för alla dess\' resurser. Det krävs därför att använda funktionen [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) som sammanfogas med dina variabel namn i resurs grupps-ID: n för att generera slumpmässiga unika värden:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Se till\'att du sammanfogar parameter-/variabel strängarna\'(contosovm) med en unik sträng\'utmatning (resourceGroup (\'). ID), eftersom detta garanterar unika och tillförlitligare av varje variabel.

De flesta resurs namn får till exempel inte börja med en siffra, men en unik sträng funktion kan returnera en sträng som börjar med en siffra. Det innebär att om du använder rå unika sträng utdata kommer distributionen inte att kunna utföras. 

Du hittar mer information om namngivnings regler och begränsningar för resurs namn i [den här artikeln](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Distributions plats

Du kan göra en test enhet tillgänglig i olika Azure-regioner. Idén är att tillåta att en användare väljer den region som ligger närmast, för att ge Beast användar upplevelse.

När test enheten skapar en instans av labbet skapar den alltid en resurs grupp i den region som väljs av en användare och kör sedan distributions mal len i den här grupp kontexten. Därför bör din mall välja distributions platsen från resurs gruppen:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Och använder sedan den här platsen för varje resurs för en speciell labb instans:

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

Du måste se till att din prenumeration har tillåtelse att distribuera alla resurser som du vill distribuera i varje region som du väljer. Du måste också se till att dina avbildningar av virtuella datorer är tillgängliga i alla regioner som du ska aktivera, annars fungerar inte distributions mal len för vissa regioner.

### <a name="outputs"></a>Utdata

Normalt med Resource Manager-mallar kan du distribuera utan att producera några utdata. Detta beror på att du vet alla värden som du använder för att fylla i mallparametrar och att du alltid kan inspektera egenskaper för alla resurser manuellt.

För test av Resource Manager-mallar är det\'viktigt att du återgår till test enheten all information, vilket krävs för att få åtkomst till labbet (webbplats-URI: er, namn på virtuell värddator, användar namn och lösen ord). Se till att alla dina namn är läsbara eftersom dessa variabler presenteras för kunden.

Det finns inga begränsningar relaterade till mallens utdata. Kom bara ihåg att konvertera alla utdatakolumner till **strängar**, så om du skickar ett objekt till utdata visas en JSON-sträng i en användare.

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

### <a name="subscription-limits"></a>Prenumerations begränsningar

En mer sak som du bör tänka på är prenumerations-och tjänst begränsningar. Om du till exempel vill distribuera upp till tio virtuella datorer med 4 kärnor måste du kontrol lera att den prenumeration du använder för ditt labb gör att du kan använda 40 kärnor.

Du hittar mer information om Azure-prenumeration och tjänst begränsningar i [den här artikeln](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). När flera test enheter kan vidtas samtidigt kontrollerar du att din prenumeration kan hantera de \# kärnor som multipliceras med det totala antalet samtidiga test enheter som kan tas.

### <a name="what-to-upload"></a>Vad som ska laddas upp

Testkör Resource Manager-mallen överförs som en zip-fil som kan innehålla olika distributions artefakter, men måste ha en fil med namnet **main-Template. JSON**. Den här filen är Azure Resource Manager distributions mal len och test enheten använder den för att instansiera ett labb.

Om du har ytterligare resurser utöver den här filen kan du referera till den som en extern resurs i mallen, eller så kan du inkludera resursen i zip-filen.

Under publicerings certifieringen utvärderar test enheten ditt distributions paket och placerar innehållet i en intern test enhets BLOB-behållare. Behållar strukturen visar strukturen för ditt distributions paket:

| Package. zip                       | BLOB-behållare för test enhet         |
|---|---|
| Main-Template. JSON                | \//\<https:\... \>. blob.Core.Windows.net/\<\... \>/main-Template.JSON  |
| mallar/lösning. JSON           | \//\<https:\... \>. blob.Core.Windows.net/\<\... \>/templates/Solution.JSON |
| skript/uppvärmnings. ps1                | \//\<https:\... \>. blob.Core.Windows.net/\<\... \>/scripts/WarmUp.ps1  |


Vi anropar en URI för den här bas-URI: n för BLOB container. Varje revidering av ditt labb har en egen BLOB-behållare och därför har varje revidering av ditt labb en egen bas-URI. Test enheten kan skicka en bas-URI för det zippade distributions paketet till din mall via mallparametrar.

## <a name="transforming-template-examples-for-test-drive"></a>Transformerar mall exempel för test enhet

Processen att aktivera en arkitektur av resurser i en Test Drive Resource Manager-mall kan vara avskräckande. För att hjälpa till att göra den här processen enklare\'har vi gjort exempel på hur du bäst [transformerar de aktuella distributions mallarna här](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Publicera en testen het

Nu när du har skapat din test enhet går det här avsnittet igenom vart och ett av de fält som krävs för att du ska kunna publicera test enheten.

![Aktivera test enheten i användar gränssnittet](./media/azure-resource-manager-test-drive/howtopub1.png)

Det första och viktigaste fältet är att växla om du vill att test enheten ska aktive ras för erbjudandet eller inte. När du väljer **Ja** visas resten av formuläret med alla obligatoriska fält som du kan fylla i. När du väljer **Nej** inaktive ras formuläret och om du återpublicerar med test enheten inaktive rad tas test enheten bort från produktionen.

OBS! om det finns test enheter som används aktivt av användarna kommer dessa test enheter att fortsätta att köras tills deras session upphör att gälla.

### <a name="details"></a>Information

Nästa avsnitt för att fylla i är information om ditt test enhets erbjudande.

![Test Drive, detaljerad information](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beskrivning –** *Kräv* att du skriver den huvudsakliga beskrivningen av vad som finns på test enheten. Kunden kommer att komma hit för att läsa vilka scenarier som test enheten kommer att täcka om din produkt. 

**Användaren är manuell –** *det här är* en djupgående genom gång av test enhets upplevelsen. Kunden öppnar den här och kan gå igenom exakt vad du vill att de ska göra i hela test enheten. Det är viktigt att det här innehållet är enkelt att förstå och följa! (Måste vara en. pdf-fil)

**Prov Drive demo video –** *rekommenderas* på liknande sätt som Användar handbok, det är bäst att ta med en video kurs om din test enhets upplevelse. Kunden kommer att titta på det här innan eller under deras testenhet och kan gå igenom exakt vad du vill att de ska göra i hela test enheten. Det är viktigt att det här innehållet är enkelt att förstå och följa!

- **Namn** – rubrik för din video
- **Link** -måste vara en inbäddad URL från ditt rör eller din video. Exempel på hur du hämtar den inbäddade URL: en finns nedan:
- **Thumbnail** – måste vara en bild med hög kvalitet (533x324). Vi rekommenderar att du tar en skärm bild av en del av test enhets upplevelsen här.

Nedan visas hur de här fälten visas för kunden under deras test enhets upplevelse.

![Plats för test enhets fält i Marketplace-erbjudandet](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Teknisk konfiguration

Nästa avsnitt där du kan fylla i är den plats där du överför din Test Drive Resource Manager-mall och anger hur speciellt dina test enhets instanser fungerar.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instanser –** *krävs* det är här du konfigurerar hur många instanser du vill, i vilken region och hur snabbt dina kunder kan få test enheten.

- **Instanser** – Välj regioner är där du väljer var du vill använda Resource Manager-mallen för test enheten i. Vi rekommenderar att du bara väljer en region där kunderna förväntar sig att finnas på.
- **Hot** Frekventa antal test enhets instanser som redan har distribuerats och väntar på åtkomst per vald region. Kunder kan komma åt dessa test enheter direkt i stället för att behöva vänta på en distribution. Kompromissen är att dessa instanser alltid körs på din Azure-prenumeration, så de kommer att ådra sig en högre drift tid. Vi rekommenderar starkt att du har **minst en aktiv instans**, eftersom de flesta av dina kunder inte vill vänta på att alla distributioner ska slutföras och att det finns en kombination av kund användningen.
- **Varm** -antalet test enhets instanser per region som har distribuerats och att den virtuella datorn har stoppats och lagrats i Azure Storage. Vänte tiden för varma instanser är långsammare än heta instanser, men drift tiden för lagring är också billigare.
- **Kall** -antal test enhets instanser per region som eventuellt kan distribueras. Kall instanser kräver hela test enhetens Resource Manager-mall för att gå igenom en distribution vid den tidpunkt då en kund begär test enheten, så den är långsammare än frekventa eller varma instanser. Men kompromissen är att du bara behöver betala för test enhetens varaktighet.

Vid det här tillfället beräknar det totala antalet möjliga samtidiga test enheter som du kommer att göra tillgängliga och kontrol lera att kvot gränsen för din prenumeration kan hantera samtidiga mängder:

**(Antal regioner markerade x aktiva instanser) + (antal regioner som valts x varma instanser) + (antal regioner valda x kalla instanser)**

**Test enhetens varaktighet (timmar) –** *obligatorisk* varaktighet för hur länge test enheten ska förbli aktiv, i \# timmar. Test enheten avslutas automatiskt när den här tids perioden är slut.

**Testa Resource Manager-mall –** *krävs* Ladda upp Resource Manager-mallen här. Detta är den fil som du skapade i föregående avsnitt ovan. Namnge huvud mal len fil: "main-Template. JSON" och se till att din Resource Manager-mall innehåller utdataparametrar för viktiga variabler som behövs. (Måste vara en. zip-fil)

**Åtkomst information –** *krävs* när en kund får sina test enheter visas åtkomst informationen för dem. De här anvisningarna är avsedda att dela användbara utdataparametrar från din Test Drive Resource Manager-mall. Om du vill inkludera utdataparametrar använder du dubbla klammerparenteser (till exempel **{{outputname}}**) och de infogas korrekt på platsen. (HTML-teckenformatering rekommenderas här för rendering på klient sidan).

### <a name="test-drive-deployment-subscription-details"></a>Information om distributions prenumeration för test enhet

Det sista avsnittet att fylla i är att kunna distribuera test enheterna automatiskt genom att ansluta din Azure-prenumeration och Azure Active Directory (AD).

![Information om distributions prenumeration för test enhet](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-prenumerations-ID –** *krävs* för att bevilja åtkomst till Azure-tjänster och Azure Portal. Prenumerationen är den plats där resursanvändningen rapporteras och tjänsterna faktureras. Om du inte redan har en **separat** Azure-prenumeration för test enheter kan du gå vidare och göra en. Du hittar prenumerations-ID: n för Azure genom att logga in på Azure Portal och navigera till prenumerationerna på den vänstra menyn. (Exempel: "a83645ac-1234-5AB6-6789-1h234g764ghty")

![Azure-prenumerationer](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-klient-ID –** *krävs* om du har ett klient-ID som redan är tillgängligt kan du hitta det\> nedan i egenskaperna-Directory-ID.

![Azure Active Directory egenskaper](./media/azure-resource-manager-test-drive/subdetails3.png)

Annars skapar du en ny klient i Azure Active Directory.

![Lista över Azure Active Directory klienter](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definiera organisation, domän och land/region för Azure AD-klienten](./media/azure-resource-manager-test-drive/subdetails5.png)

![Bekräfta valet](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App ID –** *obligatoriskt* nästa steg är att skapa och registrera ett nytt program. Vi kommer att använda det här programmet för att utföra åtgärder på din test enhets instans.

1. Navigera till den nyligen skapade katalogen eller befintlig katalog och välj Azure Active Directory i filter fönstret.
2. Sök efter "Appregistreringar" och klicka på "Lägg till"
3. Ange ett program namn.
4. Välj typ av "webbapp/API"
5. Ange ett värde för inloggnings-URL. vi vann\'t använder det fältet.
6. Klicka på Skapa.
7. När programmet har skapats går du till egenskaper –\> ange programmet som flera innehavare och tryck på Spara.

Klicka på Spara. Det sista steget är att ta tag i program-ID: t för den här registrerade appen och klistra in den i fältet test enhet här.

![Information om Azure AD-programid](./media/azure-resource-manager-test-drive/subdetails7.png)

Med tanke på att vi använder programmet för att distribuera till prenumerationen måste vi lägga till programmet som deltagare i prenumerationen. Anvisningarna för dessa är följande:

1. Gå till bladet prenumerationer och välj lämplig prenumeration som du använder endast för test enheten.
1. Klicka på **Åtkomstkontroll (IAM)**.
1. Klicka på fliken **roll tilldelningar** .  ![Lägg till en ny Access Control huvud konto](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicka på **Lägg till roll tilldelning**.
1. Ange rollen som **deltagare**.
1. Skriv namnet på Azure AD-programmet och välj det program som rollen ska tilldelas till.
    ![Lägg till behörigheterna](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicka på **Spara**.

**Azure AD App nyckel –** *obligatoriskt* det sista fältet är att generera en autentiseringsnyckel. Under nycklar lägger du till en nyckel beskrivning, ställer in varaktigheten så att den aldrig upphör att gälla och väljer sedan Spara. Det är **viktigt** att undvika att ha en utgånget nyckel, vilket innebär att din test enhet bryts i produktionen. Kopiera det här värdet och klistra in det i fältet för den obligatoriska test enheten.

![Visar nycklar för Azure AD-programmet](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har fyllt i alla dina test enhets fält går du igenom och **publicerar** om erbjudandet. När test enheten har klarat certifieringen bör du testa kund upplevelsen i för **hands versionen** av ditt erbjudande. Starta en testen het i användar gränssnittet och öppna sedan en Azure-prenumeration i Azure Portal och kontrol lera att test enheterna är fullständigt distribuerade.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Det är viktigt att Observera att du inte tar bort några test enhets instanser när de är etablerade för dina kunder, så att test enhets tjänsten rensar automatiskt dessa resurs grupper när en kund har slutförts.

När du känner dig bekvämt med ditt för hands erbjudande är det dags att **gå live**! Det finns en slutgiltig gransknings process från Microsoft när erbjudandet har publicerats för att dubbelt kontrol lera hela slut punkt till slut punkt. Om erbjudandet avvisas av någon anledning kommer vi att skicka ett meddelande till teknisk kontakt för ditt erbjudande som förklarar vad som behöver åtgärdas.

Om du har fler frågor, letar efter fel söknings råd eller vill göra en test enhet mer framgångs rik kan du gå till [vanliga frågor och svar, felsöka & bästa praxis](./marketing-and-best-practices.md).
