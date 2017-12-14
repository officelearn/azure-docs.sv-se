---
title: "Felsöka roller som inte går att starta | Microsoft Docs"
description: "Här följer några vanliga orsaker till varför en tjänst i molnet roll kan inte startas. Det finns också lösningar på problemen."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: d24a55fd3b93760035f852a24537c1cec9e4bab2
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Felsöka Cloud Service-roller som inte går att starta
Här följer några vanliga problem och lösningar som rör Azure Cloud Services roller som inte går att starta.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLL-filer som saknas eller beroenden
Sluta att svara roller och roller som reglering mellan **initierar**, **upptagen**, och **stoppar** tillstånd kan orsakas av DLL-filer eller sammansättningar saknas.

Symtom på DLL: er eller sammansättningar saknas kan vara:

* Din rollinstans Bläddra igenom **initierar**, **upptagen**, och **stoppar** tillstånd.
* Rollinstansen har flyttats till **klar** men om du navigerar till ditt webbprogram, visas inte sidan.

Det finns flera metoder för att undersöka problemen.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostisera problem med saknas DLL-filen i en webbroll
När du navigerar till en webbplats som har distribuerats på en webbplats roll och webbläsaren visar ett serverfel som liknar följande, det kan tyda på att en DLL-fil saknas.

![Serverfel i programmet '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostisera problem genom att stänga av anpassade fel
Mer information om fel kan visas genom att konfigurera web.config för webbrollen att ange anpassade felläget till avslaget och omdistribuera tjänsten.

Visa fullständig fel utan att använda Fjärrskrivbord:

1. Öppna lösningen i Microsoft Visual Studio.
2. I den **Solution Explorer**letar du upp filen web.config och öppna den.
3. Leta upp avsnittet system.web i web.config-filen och Lägg till följande rad:

    ```xml
    <customErrors mode="Off" />
    ```
4. Spara filen.
5. Paketera och distribuera om tjänsten.

När tjänsten är nytt, visas ett felmeddelande med namnet på sammansättningen eller DLL-filen saknas.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostisera problem genom att visa felet via fjärranslutning
Du kan använda Fjärrskrivbord för att få åtkomst till rollen och visa fullständig felinformation via fjärranslutning. Använd följande steg för att granska felen med hjälp av fjärrskrivbord:

1. Kontrollera att Azure SDK 1.3 eller senare är installerad.
2. Under distributionen av lösningen med hjälp av Visual Studio, välja att ”konfigurera anslutning till fjärrskrivbord”. Mer information om hur du konfigurerar fjärrskrivbordsanslutningen finns [med hjälp av fjärrskrivbord med Azure-roller](../vs-azure-tools-remote-desktop-roles.md).
3. I Microsoft Azure-portalen när instansen visar statusen **klar**till instansen. Mer information om hur du använder fjärrskrivbordet med molntjänster finns [till rollinstanser](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Logga in på den virtuella datorn med hjälp av autentiseringsuppgifterna som angavs vid konfiguration av fjärrskrivbord.
6. Öppna ett kommandofönster.
7. Skriv `IPconfig`.
8. Anteckna värdet för IPV4-adress.
9. Öppna Internet Explorer.
10. Ange adressen och namnet på webbprogrammet. Till exempel `http://<IPV4 Address>/default.aspx`.

Gå till webbplatsen returnerar nu mer utförlig felmeddelanden:

* Serverfel i programmet '/'.
* Beskrivning: Ett ohanterat undantag uppstod under körningen av den aktuella webbegäran. Granska stack-spårning för mer information om felet och var det uppstod i koden.
* Undantagsinformation: System.IO.FIleNotFoundException: Det gick inte att läsa in filen eller sammansättningen ' Microsoft.WindowsAzure.StorageClient, Version = 1.1.0.0 kultur = neutral, PublicKeyToken = 31bf856ad364e35' eller en av dess beroenden. Det går inte att hitta den angivna filen.

Exempel:

![Explicit serverfel i programmet '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostisera problem genom att använda beräkningsemulatorn
Du kan använda Microsoft Azure-beräkningsemulatorn för att diagnostisera och felsöka problem med saknade beroenden och web.config-fel.

För bästa resultat i den här metoden för diagnos bör du använda en dator eller virtuell dator som har en ren installation av Windows. Använd Windows Server 2008 R2 x64 för att simulera Azure-miljön bäst.

1. Installera den fristående versionen av den [Azure SDK](https://azure.microsoft.com/downloads/).
2. Skapa molntjänstprojektet på utveckling-datorn.
3. Navigera till mappen bin\debug på molntjänstprojektet i Utforskaren.
4. Kopiera filen .csx mapp- och .cscfg på den dator som du använder för att felsöka problem.
5. Öppna en Azure SDK-kommandotolksfönster och Skriv på ren dator `csrun.exe /devstore:start`.
6. I Kommandotolken, Skriv `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. När rollen startar visas detaljerad felinformation i Internet Explorer. Du kan också använda standard Windows felsökningsverktyg för att diagnosticera problemet.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostisera problem med hjälp av IntelliTrace
Du kan använda för arbetaren och web-roller som använder .NET Framework 4 [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), som finns i Microsoft Visual Studio Enterprise.

Följ dessa steg om du vill distribuera tjänsten med IntelliTrace aktiverad:

1. Bekräfta att Azure SDK 1.3 eller senare är installerat.
2. Distribuera lösningen med hjälp av Visual Studio. Under distributionen kan du kontrollera den **aktivera IntelliTrace för .NET 4 roller** kryssrutan.
3. När instansen startar, öppnar du den **Server Explorer**.
4. Expandera den **Azure\\molntjänster** nod och leta upp distributionen.
5. Expandera distributionen tills du ser rollinstanserna. Högerklicka på någon av instanserna.
6. Välj **visa IntelliTrace-loggar**. Den **IntelliTrace sammanfattning** öppnas.
7. Leta upp avsnittet undantag sammanfattningen. Om det finns undantag, avsnittet är märkt **Undantagsdata**.
8. Expandera den **Undantagsdata** och leta efter **System.IO.FileNotFoundException** fel som liknar följande:

![Undantagsdata saknas filen eller sammansättningen](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adressen saknas DLL: er och sammansättningar
Följ dessa steg för att åtgärda saknade DLL och sammansättningen fel:

1. Öppna lösningen i Visual Studio.
2. I **Solution Explorer**öppnar den **referenser** mapp.
3. Klicka på den sammansättning som identifieras i Windows.
4. I den **egenskaper** rutan hitta **kopiera lokala egenskapen** och ange värdet **SANT**.
5. Distribuera om Molntjänsten.

När du har kontrollerat att alla fel har åtgärdats kan du distribuera tjänsten utan att kontrollera den **aktivera IntelliTrace för .NET 4 roller** kryssrutan.

## <a name="next-steps"></a>Nästa steg
Visa mer [felsökning artiklar](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) för molntjänster.

Information om hur du felsöker cloud service rollen problem med hjälp av Azure PaaS datorn diagnostikdata finns [Kevin Williamson bloggserie](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
