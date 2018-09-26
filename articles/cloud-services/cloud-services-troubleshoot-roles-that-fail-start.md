---
title: Felsöka roller som inte vill starta | Microsoft Docs
description: Här följer några vanliga orsaker till varför en molntjänstroll kanske inte kan startas. Lösningar på problemet tillhandahålls också.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 4b4669ecdae474c8926a346ed02f40913cf67265
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093499"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Felsöka Cloud Service-roller som inte vill starta
Här följer några vanliga problem och lösningar relaterade till Azure Cloud Services-roller som inte att starta.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Saknas DLL-filer eller beroenden
Svarar inte roller och roller som cykling mellan **initierar**, **upptagen**, och **stoppar** tillstånd kan orsakas av DLL-filer eller sammansättningar saknas.

Symtomen för DLL-filer eller sammansättningar saknas kan vara:

* Rollinstansen cykluje via **initierar**, **upptagen**, och **stoppar** tillstånd.
* Din rollinstans har flyttats till **redo** men om du navigerar till ditt webbprogram, visas inte sidan.

Det finns flera metoder för att undersöka problemen.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostisera saknas DLL-problem i en webbroll
När du navigerar till en webbplats som har distribuerats i ett webb-rollen och webbläsaren visar ett serverfel som liknar följande, det kan tyda på att en DLL-fil saknas.

![Serverfel i programmet '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostisera problem genom att inaktivera anpassade fel
Fullständig information om fel kan visas genom att konfigurera web.config för web-roll att ställa in anpassade felläge på av och distribuera om tjänsten.

Visa fullständig fel utan att använda Remote Desktop:

1. Öppna lösningen i Microsoft Visual Studio.
2. I den **Solution Explorer**letar du upp filen web.config och öppna den.
3. I filen web.config, leta upp avsnittet system.web och Lägg till följande rad:

    ```xml
    <customErrors mode="Off" />
    ```
4. Spara filen.
5. Paketera om och distribuera om tjänsten.

När tjänsten är nytt, visas ett felmeddelande med namnet på sammansättningen eller DLL-filen saknas.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostisera problem genom att visa felet via en fjärranslutning
Du kan använda Fjärrskrivbord för att få åtkomst till rollen och visa fullständig information om fel via en fjärranslutning. Använd följande steg för att visa felen med hjälp av fjärrskrivbord:

1. Kontrollera att Azure SDK 1.3 eller senare är installerad.
2. Aktivera Fjärrskrivbord under distributionen av lösningen med hjälp av Visual Studio. Mer information finns i [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. I Microsoft Azure-portalen när instansen visar statusen **redo**Fjärranslut sedan till instansen. Läs mer om hur du använder fjärrskrivbordet med Cloud Services, [fjärråtkomst till rollinstanser](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Logga in till den virtuella datorn med hjälp av autentiseringsuppgifterna som angavs under konfigurationen av fjärrskrivbordet.
6. Öppna ett kommandofönster.
7. Skriv `IPconfig`.
8. Anteckna värdet för IPV4-adress.
9. Öppna Internet Explorer.
10. Skriv adressen och namnet på webbprogrammet. Till exempel `http://<IPV4 Address>/default.aspx`.

Gå till webbplatsen returnerar nu mer utförlig felmeddelanden:

* Serverfel i programmet '/'.
* Beskrivning: Ett ohanterat undantag uppstod under körningen av den aktuella webbegäran. Granska stackspårning för mer information om felet och var det uppstod i koden.
* Undantagsinformation: System.IO.FIleNotFoundException: kunde inte läsa in filen eller sammansättningen ' Microsoft.WindowsAzure.StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 ”eller något av dess beroenden. Det går inte att hitta den angivna filen.

Exempel:

![Explicit serverfel i programmet '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostisera problem med compute-emulatorn
Du kan använda Microsoft Azure-beräkningsemulatorn för att diagnostisera och felsöka problem med saknar beroenden och web.config-fel.

För bästa resultat i med hjälp av den här metoden för diagnos bör du använda en dator eller virtuell dator som har en ren installation av Windows. Bäst simulera Azure-miljön genom att använda Windows Server 2008 R2 x64.

1. Installera den fristående versionen av den [Azure SDK](https://azure.microsoft.com/downloads/).
2. På utvecklingsdator måste du skapa molntjänstprojektet.
3. Navigera till mappen bin\debug på molntjänstprojektet i Windows Explorer.
4. Kopiera den .csx mapp- och .cscfg-filen till den dator som du använder för att felsöka problem.
5. Öppna en kommandotolk i Azure SDK och Skriv på den ren datorn `csrun.exe /devstore:start`.
6. I Kommandotolken, Skriv `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. När rollen startar visas detaljerad felinformation i Internet Explorer. Du kan också använda standard Windows felsökningsverktyg för att diagnosticera problemet.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostisera problem med hjälp av IntelliTrace
Du kan använda för arbete och web-roller som använder .NET Framework 4, [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), som finns i Microsoft Visual Studio Enterprise.

Följ dessa steg om du vill distribuera tjänsten med IntelliTrace aktiverat:

1. Bekräfta att Azure SDK 1.3 eller senare är installerat.
2. Distribuera lösningen med hjälp av Visual Studio. Under distributionen, kontrollera den **aktivera IntelliTrace för .NET 4 roller** markerar du kryssrutan.
3. När instansen startas, öppnar du den **Server Explorer**.
4. Expandera den **Azure\\molntjänster** noden och leta upp distributionen.
5. Expandera distributionen tills du ser rollinstanserna. Högerklicka på någon av instanserna.
6. Välj **visa protokoly**. Den **IntelliTrace sammanfattning** öppnas.
7. Leta upp avsnittet undantag av sammanfattningen. Om det finns undantag, avsnittet är märkta **Undantagsdata**.
8. Expandera den **Undantagsdata** och leta efter **System.IO.FileNotFoundException** fel som liknar följande:

![Undantagsdata saknas filen eller sammansättningen](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Åtgärda saknade DLL-filer och sammansättningar
Följ dessa steg för att åtgärda saknade DLL och sammansättningen fel:

1. Öppna lösningen i Visual Studio.
2. I **Solution Explorer**öppnar den **referenser** mapp.
3. Klicka på den sammansättning som identifierats i felet.
4. I den **egenskaper** fönstret hitta **kopiera lokal egenskap** och ange värdet **SANT**.
5. Distribuera om Molntjänsten.

När du har kontrollerat att alla fel har korrigerats kan du distribuera tjänsten utan att kontrollera den **aktivera IntelliTrace för .NET 4 roller** markerar du kryssrutan.

## <a name="next-steps"></a>Nästa steg
Visa mer [felsökningsartiklar](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) för molntjänster.

Om du vill lära dig mer om felsökning av problem med cloud service rollen genom att använda diagnostikdata för Azure PaaS-dator, se [Kevin Williamson bloggserie](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
