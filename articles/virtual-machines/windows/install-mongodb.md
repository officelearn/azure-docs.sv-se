---
title: Installera MongoDB på en Windows-dator i Azure | Microsoft Docs
description: Lär dig hur du installerar MongoDB på en Azure-dator som kör Windows Server 2012 R2 som skapas med Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a45d6a6064173cea7ed15065ab3464718cc8578e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888829"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installera och konfigurera MongoDB på en Windows-dator i Azure
[MongoDB](http://www.mongodb.org) är en populära öppen källkod, högpresterande NoSQL-databas. Den här artikeln visar hur du installerar och konfigurerar MongoDB på en Windows Server 2016-dator (VM) i Azure. Du kan också [installerar MongoDB på en Linux-VM i Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du installerar och konfigurerar MongoDB, måste du skapa en virtuell dator, och vi rekommenderar lägga till en datadisk till den. Se följande artiklar för att skapa en virtuell dator och lägga till en datadisk:

* Skapa en Windows Server-dator med hjälp av [Azure-portalen](quick-create-portal.md) eller [Azure PowerShell](quick-create-powershell.md).
* Anslut en datadisk till en Windows Server-dator med hjälp av [Azure-portalen](attach-managed-disk-portal.md) eller [Azure PowerShell](attach-disk-ps.md).

Att börja installera och konfigurera MongoDB, [logga in på din Windows Server-VM](connect-logon.md) med hjälp av fjärrskrivbord.

## <a name="install-mongodb"></a>Installera MongoDB
> [!IMPORTANT]
> MongoDB säkerhetsfunktioner, till exempel autentisering och IP-adress bindningen är inte aktiverade som standard. Säkerhetsfunktioner måste vara aktiverad innan du distribuerar MongoDB till en produktionsmiljö. Mer information finns i [MongoDB säkerhet och autentisering](http://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. När du har anslutit till den virtuella datorn med hjälp av fjärrskrivbord kan du öppna Internet Explorer från Aktivitetsfältet.
2. Välj **Använd rekommenderade inställningar för säkerhet, sekretess och kompatibilitet** när Internet Explorer först öppnas, och klickar på **OK**.
3. Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat som standard. Lägg till MongoDB-webbplatsen i listan över tillåtna webbplatser:
   
   * Välj den **verktyg** i det övre högra hörnet.
   * I **Internetalternativ**väljer den **Security** fliken och välj sedan den **tillförlitliga platser** ikon.
   * Klicka på den **platser** knappen. Lägg till *https://\*. mongodb.com* i listan över betrodda platser och stäng sedan dialogrutan.
     
     ![Konfigurera säkerhetsinställningar för Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Bläddra till den [MongoDB - hämtningar](http://www.mongodb.com/downloads) sidan (http://www.mongodb.com/downloads).
5. Om det behövs, Välj den **gruppservern** edition och välj sedan det senaste aktuella stabilt viktig för*Windows Server 2008 R2 64-bitars och senare*. För att hämta installationsprogrammet, klickar du på **DOWNLOAD (msi)**.
   
    ![Ladda ned installationsprogrammet för MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Kör installationsprogrammet när hämtningen är slutförd.
6. Läs och acceptera licensavtalet. När du uppmanas väljer **Slutför** installera.
7. Om du vill kan välja du att även installera kompass, ett grafiskt gränssnitt för MongoDB.
8. Klicka på den sista skärmen **installera**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurera den virtuella datorn och MongoDB
1. Sökvägsvariabler uppdateras inte med MongoDB-installationsprogrammet. Utan MongoDB `bin` plats i path-variabeln, måste du ange den fullständiga sökvägen varje gång du använder en MongoDB körbar fil. Att lägga till platsen i path-variabeln:
   
   * Högerklicka på den **starta** menyn och välj **System**.
   * Klicka på **avancerade systeminställningar**, och klicka sedan på **miljövariabler**.
   * Under **systemvariabler**väljer **sökväg**, och klicka sedan på **redigera**.
     
     ![Konfigurera sökvägsvariabler](./media/install-mongodb/configure-path-variables.png)
     
     Lägga till sökvägen till din MongoDB `bin` mapp. MongoDB installeras vanligtvis i *C:\Program Files\MongoDB*. Kontrollera installationssökvägen på den virtuella datorn. I följande exempel läggs standard installationsplatsen för MongoDB till den `PATH` variabeln:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Se till att lägga till ledande semikolon (`;`) att indikera att du lägger till en plats för att din `PATH` variabeln.

2. Skapa MongoDB-data och loggfiler kataloger på din datadisk. Från den **starta** menyn och välj **kommandotolk**. I följande exempel skapar katalogerna på enhet F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Starta en MongoDB-instans med följande kommando, justera sökvägen till dina data och logga kataloger i enlighet med detta:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Det kan ta flera minuter för MongoDB att allokera journalfilerna och starta lyssna efter anslutningar. Alla loggmeddelanden dirigeras till den *F:\MongoLogs\mongolog.log* filen som `mongod.exe` server startar och allokerar journalfiler.
   
   > [!NOTE]
   > Kommandotolken förblir fokuserad på den här uppgiften när MongoDB-instansen körs. Lämna Kommandotolken öppen fortsätta att köra MongoDB. Eller installera MongoDB som tjänst, som beskrivs i nästa steg.

4. En mer stabil MongoDB-upplevelse, installera den `mongod.exe` som en tjänst. Skapa en tjänst innebär att du inte behöver lämna Kommandotolken som körs varje gång som du vill använda MongoDB. Skapa tjänsten enligt följande, justeras efter sökvägen till dina data och loggfiler kataloger:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Föregående kommando skapar en tjänst med namnet MongoDB, med en beskrivning av ”Mongo DB”. Följande parametrar anges också:
   
   * Den `--dbpath` alternativet anger platsen för data-katalog.
   * Den `--logpath` alternativet måste användas för att ange en loggfil, eftersom tjänsten som körs inte har ett kommandofönster för att visa utdata.
   * Den `--logappend` alternativet anger att en omstart av tjänsten gör att utdata ska läggas till den befintliga loggfilen.
   
   Om du vill starta tjänsten MongoDB, kör du följande kommando:
   
    ```
    net start MongoDB
    ```
   
    Mer information om hur du skapar en MongoDB-tjänsten finns i [konfigurerar en Windows-tjänst för MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testa MongoDB-instans
Med MongoDB som körs som en enskild instans eller installeras som en tjänst, kan du nu börja skapa och använda dina databaser. Starta administrativa MongoDB-gränssnittet genom att öppna en annan kommandotolksfönster från den **starta** -menyn och ange följande kommando:

```
mongo  
```

Du kan lista databaser med den `db` kommando. Infoga data på följande sätt:

```
db.foo.insert( { a : 1 } )
```

Söka efter data på följande sätt:

```
db.foo.find()
```

Utdata ser ut ungefär så här:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Avsluta den `mongo` konsolen på följande sätt:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurera brandväggen och regler för Nätverkssäkerhetsgrupp
Nu när MongoDB är installerade och körs kan du öppna en port i Windows-brandväggen så att du kan fjärransluta till MongoDB. Om du vill skapa en ny inkommande regel för att tillåta TCP-port 27017, öppna en administrativ PowerShell-kommandotolk och ange följande kommando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Du kan också skapa regeln med hjälp av den **Windows-brandväggen med avancerad säkerhet** grafiskt hanteringsverktyg. Skapa en ny inkommande regel för att tillåta TCP-port 27017.

Om det behövs skapar du en regel för Nätverkssäkerhetsgrupp för att tillåta åtkomst till MongoDB från utanför det befintliga undernätet i Azure-nätverk. Du kan skapa regler för Nätverkssäkerhetsgruppen med hjälp av den [Azure-portalen](nsg-quickstart-portal.md) eller [Azure PowerShell](nsg-quickstart-powershell.md). Precis som med Windows-brandväggsreglerna Tillåt TCP-port 27017 till din MongoDB VM virtuella nätverksgränssnitt.

> [!NOTE]
> TCP-port 27017 är standardporten som används av MongoDB. Du kan ändra den här porten med hjälp av den `--port` parameter när du startar `mongod.exe` manuellt eller från en tjänst. Om du ändrar porten kan du se till att uppdatera Windows-brandväggen och Nätverkssäkerhetsgruppen reglerna i föregående steg.


## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att installera och konfigurera MongoDB på den virtuella Windows-datorn. Du kan nu komma åt MongoDB på den virtuella datorn i Windows, genom att följa de avancerade ämnena i den [MongoDB-dokumentation](https://docs.mongodb.com/manual/).

