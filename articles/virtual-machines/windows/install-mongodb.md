---
title: Installera MongoDB på en Virtuell Windows-dator i Azure
description: Lär dig hur du installerar MongoDB på en Azure VM som kör Windows Server 2012 R2 som skapats med Resurshanterarens distributionsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 37c1b58d364e7eadb33803ce7eac1f2b956ec1b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038552"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installera och konfigurera MongoDB på en Windows-virtuell dator i Azure
[MongoDB](https://www.mongodb.org) är en populär nosql-databas med öppen källkod. Den här artikeln hjälper dig att installera och konfigurera MongoDB på en virtuell dator (VM) för Windows Server 2016 (VM) i Azure. Du kan också [installera MongoDB på en Virtuell Linux-dator i Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Krav
Innan du installerar och konfigurerar MongoDB måste du skapa en virtuell dator och helst lägga till en datadisk i den. Se följande artiklar för att skapa en virtuell dator och lägga till en datadisk:

* Skapa en virtuell Windows Server-dator med [Azure-portalen](quick-create-portal.md) eller [Azure PowerShell](quick-create-powershell.md).
* Bifoga en datadisk till en virtuell Windows Server-dator med [Azure-portalen](attach-managed-disk-portal.md) eller [Azure PowerShell](attach-disk-ps.md).

Om du vill börja installera och konfigurera MongoDB [loggar du in på den virtuella datorn för Windows Server](connect-logon.md) med fjärrskrivbord.

## <a name="install-mongodb"></a>Installera MongoDB
> [!IMPORTANT]
> MongoDB-säkerhetsfunktioner, till exempel autentisering och IP-adressbindning, är inte aktiverade som standard. Säkerhetsfunktioner bör aktiveras innan MongoDB distribueras till en produktionsmiljö. Mer information finns i [MongoDB-säkerhet och autentisering](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. När du har anslutit till den virtuella datorn med Fjärrskrivbord öppnar du Internet Explorer från Aktivitetsfältet.
2. Välj **Använd rekommenderade inställningar för säkerhet, sekretess och kompatibilitet** när Internet Explorer öppnas och klicka på **OK**.
3. Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverad som standard. Lägg till MongoDB webbplats till listan över tillåtna webbplatser:
   
   * Välj **ikonen Verktyg** i det övre högra hörnet.
   * Välj fliken **Säkerhet** i **Internetalternativ**och välj sedan ikonen **Betrodda platser.**
   * Klicka på knappen **Webbplatser.** Lägg till *https://\*.mongodb.com* i listan över betrodda platser och stäng sedan dialogrutan.
     
     ![Konfigurera säkerhetsinställningar för Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Bläddra till [sidan MongoDB - Nedladdningar](https://www.mongodb.com/downloads) (https://www.mongodb.com/downloads).
5. Om det behövs väljer du **Community Server-utgåvan** och väljer sedan den senaste stabila versionen för*Windows Server 2008 R2 64-bitars och senare*. Om du vill hämta installationsprogrammet klickar du på **LADDA NED (msi)**.
   
    ![Ladda ner MongoDB installationsprogrammet](./media/install-mongodb/download-mongodb.png)
   
    Kör installationsprogrammet när hämtningen är klar.
6. Läs och acceptera licensavtalet. När du uppmanas att göra det väljer du **Slutför** installation.
7. Om så önskas kan du välja att också installera Compass, ett grafiskt gränssnitt för MongoDB.
8. Klicka på **Installera**på den sista skärmen .

## <a name="configure-the-vm-and-mongodb"></a>Konfigurera den virtuella datorn och MongoDB
1. Sökvägsvariablerna uppdateras inte av MongoDB-installationsprogrammet. Utan MongoDB-platsen `bin` i sökvägsvariabeln måste du ange den fullständiga sökvägen varje gång du använder en MongoDB-körbar. Så här lägger du till platsen i sökvägsvariabeln:
   
   * Högerklicka på **Start-menyn** och välj **System**.
   * Klicka på **Avancerade systeminställningar**och sedan på **Miljövariabler**.
   * Under **Systemvariabler**väljer du **Bana**och klickar sedan på **Redigera**.
     
     ![Konfigurera PATH-variabler](./media/install-mongodb/configure-path-variables.png)
     
     Lägg till sökvägen till `bin` mappen MongoDB. MongoDB installeras vanligtvis i *C:\Program Files\MongoDB*. Verifiera installationssökvägen på den virtuella datorn. I följande exempel läggs standardplatsen mongoDb till i variabeln: `PATH`
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Var noga med att lägga`;`till den ledande semikolon ( `PATH` ) för att ange att du lägger till en plats i din variabel.

2. Skapa MongoDB-data och logga kataloger på din datadisk. Välj **Kommandotolken**på **Start-menyn** . I följande exempel skapas katalogerna på enhet F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Starta en MongoDB-förekomst med följande kommando och justera sökvägen till dina data- och loggkataloger därefter:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Det kan ta flera minuter för MongoDB att allokera journalfilerna och börja lyssna efter anslutningar. Alla loggmeddelanden dirigeras till *filen F:\MongoLogs\mongolog.log* när `mongod.exe` servern startar och allokerar journalfiler.
   
   > [!NOTE]
   > Kommandotolken fokuserar på den här uppgiften medan Din MongoDB-instans körs. Lämna kommandotolksfönstret öppet för att fortsätta köra MongoDB. Eller installera MongoDB som tjänst, som beskrivs i nästa steg.

4. För en mer robust MongoDB-upplevelse, installera `mongod.exe` som en tjänst. Att skapa en tjänst innebär att du inte behöver lämna en kommandotolk som körs varje gång du vill använda MongoDB. Skapa tjänsten på följande sätt och justera sökvägen till dina data- och loggkataloger därefter:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Föregående kommando skapar en tjänst med namnet MongoDB, med en beskrivning av "Mongo DB". Följande parametrar har också angetts:
   
   * Alternativet `--dbpath` anger platsen för datakatalogen.
   * Alternativet `--logpath` måste användas för att ange en loggfil, eftersom den tjänst som körs inte har något kommandofönster för att visa utdata.
   * Alternativet `--logappend` anger att en omstart av tjänsten gör att utdata läggs till i den befintliga loggfilen.
   
   Starta mongoDb-tjänsten genom att köra följande kommando:
   
    ```
    net start MongoDB
    ```
   
    Mer information om hur du skapar Tjänsten MongoDB finns i [Konfigurera en Windows-tjänst för MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testa MongoDB-instansen
Med MongoDB körs som en enda instans eller installeras som en tjänst, kan du nu börja skapa och använda dina databaser. Om du vill starta det administrativa mongoDb-skalet öppnar du ett annat kommandotolksfönster på **Start-menyn** och anger följande kommando:

```
mongo  
```

Du kan lista databaserna `db` med kommandot. Infoga vissa data enligt följande:

```
db.foo.insert( { a : 1 } )
```

Sök efter data enligt följande:

```
db.foo.find()
```

Utdata ser ut ungefär så här:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Avsluta `mongo` konsolen enligt följande:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurera regler för brandvägg och nätverkssäkerhetsgrupp
Nu när MongoDB är installerat och körs, öppna en port i Windows-brandväggen så att du kan fjärransluta till MongoDB. Om du vill skapa en ny inkommande regel för att tillåta TCP-port 27017 öppnar du en administrativ PowerShell-fråga och anger följande kommando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Du kan också skapa regeln med hjälp av **Windows-brandväggen med** grafisk hanteringsverktyg för avancerad säkerhet. Skapa en ny inkommande regel för att tillåta TCP-port 27017.

Om det behövs skapar du en regel för nätverkssäkerhetsgrupp för att tillåta åtkomst till MongoDB utanför det befintliga azure-virtuella nätverksundernätet. Du kan skapa reglerna för nätverkssäkerhetsgruppen med hjälp av [Azure-portalen](nsg-quickstart-portal.md) eller [Azure PowerShell](nsg-quickstart-powershell.md). Precis som med Reglerna för Windows-brandväggen tillåter du TCP-port 27017 till det virtuella nätverksgränssnittet för din Virtuella MongoDB-dator.

> [!NOTE]
> TCP-port 27017 är standardporten som används av MongoDB. Du kan ändra den `--port` här porten `mongod.exe` med hjälp av parametern när du startar manuellt eller från en tjänst. Om du ändrar porten måste du uppdatera reglerna för Windows-brandväggen och nätverkssäkerhetsgruppen i föregående steg.


## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig hur du installerar och konfigurerar MongoDB på din Virtuella Windows-dator. Du kan nu komma åt MongoDB på din Virtuella Windows-dator genom att följa de avancerade ämnena i [MongoDB-dokumentationen](https://docs.mongodb.com/manual/).

