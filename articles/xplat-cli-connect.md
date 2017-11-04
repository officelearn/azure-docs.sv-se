---
title: "Logga in till Azure från CLI | Microsoft Docs"
description: "Ansluta till din Azure-prenumeration från Azure-kommandoradsgränssnittet (Azure CLI) för Mac, Linux och Windows"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Logga in till Azure från Azure CLI
Azure CLI är öppen källkod, plattformsoberoende kommandon för att arbeta med Azure-resurser. Den här artikeln beskrivs olika sätt att ange dina autentiseringsuppgifter för Azure-konto för att ansluta Azure CLI på Azure-prenumerationen:

* Kör den `azure login` CLI-kommando för autentisering via Azure Active Directory. Den här metoden ger dig tillgång till CLI-kommandona i både [kommandot lägen](#cli-command-modes). När du kör kommandot utan ytterligare alternativ `azure login` uppmanas du att fortsätta att logga in interaktivt i en webbportal. För ytterligare `azure login` kommandot Alternativ, finns i den här artikeln eller Skriv `azure login --help`.
* Om du bara behöver använda Azure Service Management läge CLI-kommandona (rekommenderas inte för de flesta nya distributioner) kan du hämta och installera en publicera inställningsfil på datorn.

Om du inte redan har installerat CLI, se [installerar Azure CLI](cli-install-nodejs.md). Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](http://azure.microsoft.com/free/) på ett par minuter.

Bakgrundsinformation om annat konto identiteter och Azure-prenumerationer finns [hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="scenario-1-azure-login-with-interactive-login"></a>Scenario 1: azure-inloggning med interaktiv inloggning
Vissa konton CLI, måste du köra `azure login` och sedan fortsätta inloggningen med en webbläsare via en webbportal, en process som kallas *interaktiv inloggning*. En vanlig orsak är när du har ett arbets- eller skolkonto konto (kallas även en *organisationskonto*) som har konfigurerats att kräva multifaktorautentisering. Även använda interaktiv inloggning med ditt Microsoft-konto när du vill använda kommandon för Resource Manager-läge.

Interaktiv inloggning är enkel: typen `azure login` --utan alternativ--som visas i följande exempel:

```
azure login
```                                                                                             

Utdata visas något som liknar följande:

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
Kopiera kod som erbjuds i kommandoutdata och öppna en webbläsare till http://aka.ms/devicelogin eller andra sida om anges. (Du kan öppna en webbläsare på samma dator eller på en annan dator eller enhet). Ange koden och du uppmanas att ange användarnamn och lösenord för identiteten som du vill använda. När den här processen är klar slutför inloggningen i Kommandotolken. Det kan se ut ungefär så:

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> Med interaktiv inloggning utförs autentisering och auktorisering med Azure Active Directory. Om du använder en Microsoft-kontoidentitet inloggningen har åtkomst till din Azure Active Directory-standarddomän. (Om du har registrerat dig för ett kostnadsfritt Azure-konto, Azure Active Directory skapas automatiskt en standarddomän för ditt konto.)
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>Scenario 2: azure logga in med ett användarnamn och lösenord
Använd den `azure login` med användarnamnet (`-u`) att autentisera när du vill använda ett arbets- eller Skol-konto som inte kräver flerfunktionsautentisering. Du uppmanas på kommandoraden för lösenordet (eller alternativt kan du skicka lösenordet som en extra parameter för den `azure login` kommandot). I följande exempel skickas användarnamnet för ett organisationskonto:

    azure login -u myUserName@contoso.onmicrosoft.com

Du uppmanas sedan att ange ditt lösenord:

    info:    Executing command login
    Password: *********

Inloggningen sedan är klar.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

Om det här är din första gången logga in med autentiseringsuppgifterna uppmanas du att bekräfta att du vill cachelagra en autentiseringstoken. Det här meddelandet kan också uppstå om du tidigare använt den `azure logout` kommando (beskrivs senare i artikeln). Om du vill kringgå det här meddelandet för automatiseringsscenarier kör `azure login` med den `-q` parameter.

## <a name="scenario-3-azure-login-with-a-service-principal"></a>Scenario 3: azure logga in med ett huvudnamn för tjänsten
Om du skapar ett huvudnamn för tjänsten för ett Active Directory-program och tjänstens huvudnamn har behörighet för din prenumeration, kan du använda den `azure login` kommando för att autentisera tjänstens huvudnamn. Beroende på ditt scenario kan du ange autentiseringsuppgifter för tjänstens huvudnamn som explicit parametrar av den `azure login` kommando. Följande kommando skickar till exempel tjänstens huvudnamn och Active Directory klient-ID:

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

Sedan uppmanas du att ange lösenordet. Du kan också ange autentiseringsuppgifter via en CLI-kod i skript eller ett program eller använda ett certifikat för att autentisera tjänstens huvudnamn icke-interaktivt för automatiseringsscenarier. Mer information och exempel finns [autentiserar ett huvudnamn för tjänsten med Azure Resource Manager](resource-group-authenticate-service-principal-cli.md).

## <a name="scenario-4-use-a-publish-settings-file"></a>Scenario 4: Använda en publicera inställningsfil
Om du bara behöver använda Azure Service Management läge CLI-kommandona (till exempel för att distribuera virtuella Azure-datorer i den klassiska distributionsmodellen) kan du ansluta med en fil med inställningar i Publicera. Den här metoden installerar ett certifikat på den lokala datorn där du kan utföra hanteringsuppgifter för så länge prenumerationen och certifikatet är giltigt.

* **Hämta filen publicera** för ditt konto, kontrollera att CLI är i Service Management-läge genom att skriva `azure config mode asm`. Kör följande kommando:

        azure account download

Detta öppnar din standardwebbläsare och du uppmanas att logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com). När du loggar in, en `.publishsettings` filhämtningar. Notera var filen sparas.

> [!NOTE]
> Om ditt konto är kopplat till flera innehavare av Azure Active Directory, kan du uppmanas att välja vilka Active Directory som du vill hämta en fil med publicera inställningar för.
>
>

När valts med hjälp av hämtningssidan, eller genom att gå till den klassiska Azure-portalen, blir det standardvärde som används av sidan klassiska portal och hämta valda Active Directory. När ett standardvärde har upprättats kan du se texten '**Klicka här om du vill gå tillbaka till valsidan av**' överst på hämtningssidan. Använd angivna länken om du vill gå tillbaka till sidan.

* **Importera inställningsfilen publicera**, kör du följande kommando:

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> När du har importerat dina Publiceringsinställningar, bör du ta bort den `.publishsettings` filen. Det krävs inte längre av Azure CLI och utgör en säkerhetsrisk som kan användas för att få åtkomst till din prenumeration.
>
>

## <a name="cli-command-modes"></a>CLI kommandolägen
Azure CLI tillhandahåller två kommandolägen för att arbeta med Azure-resurser med olika uppsättningar:

* **Resource Manager-läget** – för att arbeta med Azure-resurser i Resource Manager-distributionsmodellen. För att ange det här läget kör `azure config mode arm`.
* **Service Management-läge** – för att arbeta med Azure-resurser i den klassiska distributionsmodellen. För att ange det här läget kör `azure config mode asm`.

När installerade, är den aktuella versionen av CLI i Resource Manager-läge.

> [!NOTE]
> I Resource Manager och Service Management-läge kan inte anges samtidigt. Det vill säga kan resurser som skapats i ett läge inte hanteras från andra läge.
>
>

## <a name="multiple-subscriptions"></a>Flera prenumerationer
Om du har flera Azure-prenumerationer, ger ansluta till Azure åtkomst till alla prenumerationer som är kopplade till dina autentiseringsuppgifter. En prenumeration är valt som standard och används av Azure CLI när du utför åtgärder. Du kan visa prenumerationer, inklusive aktuell standard-prenumeration, med hjälp av `azure account list` kommando. Det här kommandot returnerar informationen som liknar följande:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

I listan ovan i **aktuella** kolumnen visas aktuell standard-prenumeration som Azure-sub-1. Om du vill ändra standard-prenumerationen, använder den `azure account set` kommando och ange den prenumeration som du vill använda som standard. Exempel:

    azure account set Azure-sub-2

Detta ändrar standard prenumerationen till Azure-sub-2.

> [!NOTE]
> Ändra standard prenumerationen träder i kraft omedelbart och är en global ändring; nya Azure CLI-kommandona använder om du kör dem från samma kommandoradsverktyget instans eller en annan instans den nya standard-prenumerationen.
>
>

Om du vill använda en icke-standard-prenumeration med Azure CLI, men inte vill ändra aktuell standard, kan du använda den `--subscription` alternativet för kommandot och ange namnet på den prenumeration som du vill använda för åtgärden.

När du är ansluten till din Azure-prenumeration kan börja du använda Azure CLI-kommandona ska fungera med Azure-resurser.

## <a name="storage-of-cli-settings"></a>Lagring av CLI-inställningar
Om du loggar in med den `azure login` kommando eller importera Publiceringsinställningar, din CLI profil och loggar lagras i en `.azure` directory finns i din `user` directory. Din `user` directory skyddas av operativsystemet. Vi rekommenderar dock att du vidta ytterligare åtgärder för att kryptera din `user` directory. Så här kan du göra det:

* Ändra egenskaper för directory i Windows, eller använda BitLocker.
* På Mac aktivera FileVault för katalogen.
* I Ubuntu använder du katalogfunktionen Encrypted Home. Andra Linux-distributioner erbjuder liknande funktioner.

## <a name="logging-out"></a>Logga ut
Om du vill logga ut, använder du följande kommando:

    azure logout -u <username>

Om prenumerationer som är associerat med kontot verifieras endast med Active Directory, logga ut tar bort information om prenumerationen från den lokala profilen. Men om en publicera inställningsfil även importerades för prenumerationerna loggar endast tar bort Active Directory relaterad information från den lokala profilen.

## <a name="next-steps"></a>Nästa steg
* Om du vill använda Azure CLI-kommandon finns [Azure CLI-kommandona i Resource Manager-läget](virtual-machines/azure-cli-arm-commands.md) och [Azure CLI-kommandon i Service Management-läge](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* Om du vill veta mer om Azure CLI, ladda ned källkoden rapportera problem eller bidra till projektet, finns det [GitHub-lagringsplatsen för Azure CLI](https://github.com/azure/azure-xplat-cli).
* Om du får problem med att använda Azure CLI eller Azure finns i [Azure forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).
