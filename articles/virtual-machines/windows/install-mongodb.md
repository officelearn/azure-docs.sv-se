---
title: "Installera MongoDB på en Windows-dator i Azure | Microsoft Docs"
description: "Lär dig hur du installerar MongoDB på en Azure-dator som kör Windows Server 2012 R2 som skapats med Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: db1a550b9273925b304fe4280f2a1b0e115f856d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installera och konfigurera MongoDB på en Windows-dator i Azure
[MongoDB](http://www.mongodb.org) är en populär öppen källkod, högpresterande NoSQL-databas. Den här artikeln visar hur du installerar och konfigurerar MongoDB på en Windows Server 2012 R2 virtuell dator (VM) i Azure. Du kan också [installera MongoDB på en Linux-VM i Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Krav
Innan du installerar och konfigurerar MongoDB, måste du skapa en virtuell dator och du bör lägga till en datadisk i den. Se följande artiklar för att skapa en virtuell dator och lägger till en datadisk:

* Skapa en virtuell Windows Server-dator med hjälp av [Azure-portalen](quick-create-portal.md) eller [Azure PowerShell](quick-create-powershell.md).
* Ansluta en datadisk till en virtuell Windows Server-dator med hjälp av [Azure-portalen](attach-managed-disk-portal.md) eller [Azure PowerShell](attach-disk-ps.md).

Att börja installera och konfigurera MongoDB [logga in på Windows Server-VM](connect-logon.md) med hjälp av fjärrskrivbord.

## <a name="install-mongodb"></a>Installera MongoDB
> [!IMPORTANT]
> MongoDB säkerhetsfunktioner, till exempel autentisering och IP-adress bindningen är inte aktiverade som standard. Säkerhetsfunktioner ska aktiveras innan du distribuerar MongoDB till en produktionsmiljö. Mer information finns i [MongoDB säkerhet och autentisering](http://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. När du har anslutit till den virtuella datorn med hjälp av fjärrskrivbord, öppna Internet Explorer från den **starta** menyn på den virtuella datorn.
2. Välj **Använd rekommenderade inställningar för säkerhet, sekretess och kompatibilitet** när Internet Explorer först öppnar och på **OK**.
3. Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat som standard. Lägg till MongoDB-webbplatsen i listan över tillåtna platser:
   
   * Välj den **verktyg** i det övre högra hörnet.
   * I **Internetalternativ**, Välj den **säkerhet** och välj sedan den **tillförlitliga platser** ikon.
   * Klicka på den **platser** knappen. Lägg till *https://\*. mongodb.org* i listan över betrodda platser och Stäng dialogrutan.
     
     ![Konfigurera säkerhetsinställningar för Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Bläddra till den [MongoDB - hämtningar](http://www.mongodb.org/downloads) sida (http://www.mongodb.org/downloads).
5. Om det behövs, Välj den **Community Server** edition och välj sedan det senaste aktuella stabilt versionen för Windows Server 2008 R2 64-bitars och senare. För att hämta installationsprogrammet, klickar du på **DOWNLOAD (msi)**.
   
    ![Hämta MongoDB installer](./media/install-mongodb/download-mongodb.png)
   
    Kör installationsprogrammet när hämtningen är slutförd.
6. Läs och acceptera licensavtalet (EULA). När du uppmanas, välja **Slutför** installera.
7. Klicka på den sista sidan **installera**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurera den virtuella datorn och MongoDB
1. Path-variabler uppdateras inte av installationsprogrammet MongoDB. Utan MongoDB `bin` plats i path-variabeln, måste du ange den fullständiga sökvägen varje gång du använder en körbar fil MongoDB. Lägga till platsen i path-variabeln:
   
   * Högerklicka på den **starta** menyn och välj **System**.
   * Klicka på **avancerade systeminställningar**, och klicka sedan på **miljövariabler**.
   * Under **systemvariabler**väljer **sökväg**, och klicka sedan på **redigera**.
     
     ![Konfigurera sökvägsvariabler](./media/install-mongodb/configure-path-variables.png)
     
     Lägg till sökvägen till din MongoDB `bin` mapp. MongoDB installeras normalt i *C:\Program Files\MongoDB*. Kontrollera installationssökvägen på den virtuella datorn. I följande exempel läggs standard MongoDB installera platsen till den `PATH` variabeln:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.2\bin
     ```
     
     > [!NOTE]
     > Se till att lägga till inledande semikolon (`;`) att indikera att du lägger till en plats för att din `PATH` variabeln.

2. Skapa MongoDB data och loggfilen kataloger på hårddisken data. Från den **starta** väljer du **kommandotolk**. I följande exempel skapa kataloger på enhet F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Starta en MongoDB-instansen med följande kommando, justera sökvägen till dina data och logga kataloger i enlighet med detta:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Det kan ta flera minuter för MongoDB att allokera journal-filer och börja lyssna efter anslutningar. Alla loggmeddelanden dirigeras till den *F:\MongoLogs\mongolog.log* filen som `mongod.exe` servern startar och allokerar journal-filer.
   
   > [!NOTE]
   > Kommandotolken förblir fokuserar på den här uppgiften när MongoDB-instansen körs. Lämna Kommandotolken öppen fortsätta att köra MongoDB. Eller installera MongoDB som tjänst enligt anvisningarna i nästa steg.

4. En mer robusta MongoDB-upplevelse, installera den `mongod.exe` som en tjänst. När du skapar en tjänst innebär du inte behöver lämna Kommandotolken körs varje gång som du vill använda MongoDB. Skapa tjänsten på följande sätt justeras sökvägen till dina data och loggfilen kataloger efter:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```
   
    Föregående kommando skapar en tjänst med namnet MongoDB, med en beskrivning av ”Mongo databas”. Följande parametrar anges också:
   
   * Den `--dbpath` alternativet anger platsen för datakatalog.
   * Den `--logpath` alternativet måste användas för att ange en loggfil, eftersom tjänsten körs inte har ett kommandofönster om du vill visa utdata.
   * Den `--logappend` alternativet anger att en omstart av tjänsten gör att utdata ska läggas till i den befintliga loggfilen.
   
   Om du vill starta tjänsten MongoDB, kör du följande kommando:
   
    ```
    net start MongoDB
    ```
   
    Mer information om hur du skapar MongoDB-tjänsten finns [konfigurera en Windows-tjänst för MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testa MongoDB-instansen
Med MongoDB körs som en enda instans eller installeras som en tjänst, kan du nu börja skapa och använda dina databaser. Om du vill starta det administrativa gränssnittet MongoDB, öppna en annan kommandotolk från den **starta** -menyn och ange följande kommando:

```
mongo  
```

Du kan visa en lista över databaser med den `db` kommando. Infoga data på följande sätt:

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

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurera brandväggen och Nätverkssäkerhetsgruppen regler
Nu när MongoDB är installerade och körs, öppna en port i Windows-brandväggen så att du kan fjärransluta till MongoDB. Om du vill skapa en ny inkommande regel för att tillåta TCP-port 27017 öppna en administrativ PowerShell-Kommandotolken och ange följande kommando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Du kan också skapa regeln med hjälp av den **Windows-brandväggen med avancerad säkerhet** grafiskt hanteringsverktyg. Skapa en ny inkommande regel för att tillåta TCP-port 27017.

Skapa en säkerhetsgrupp för nätverk-regel för att tillåta åtkomst till MongoDB från utanför det befintliga virtuella Azure-nätverket undernätet vid behov. Du kan skapa Nätverkssäkerhetsgruppen-regler med hjälp av den [Azure-portalen](nsg-quickstart-portal.md) eller [Azure PowerShell](nsg-quickstart-powershell.md). Precis som med Windows-brandväggsreglerna Tillåt TCP-port 27017 till det virtuella nätverksgränssnittet av MongoDB-VM.

> [!NOTE]
> TCP-port 27017 är standardporten som används av MongoDB. Du kan ändra den här porten med hjälp av den `--port` parameter när du startar `mongod.exe` manuellt eller från en tjänst. Om du ändrar porten måste du se till att uppdatera Windows-brandväggen och Nätverkssäkerhetsgruppen reglerna i föregående steg.


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du installerar och konfigurerar MongoDB på din Windows-VM. Du kan nu komma åt MongoDB på den virtuella datorn i Windows, genom att följa de avancerade ämnena i det [MongoDB-dokumentation](https://docs.mongodb.com/manual/).

