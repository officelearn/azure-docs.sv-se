---
title: Azure RemoteApp-licensiering | Microsoft Docs
description: "Läs om hur det fungerar med licensiering i Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f4429461139a41644f87185bbb06b1f6a8011345


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Så här fungerar det med licensiering i Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp upphör att gälla. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.
> 
> 

Du har nu konfigurerat Azure RemoteApp-tjänsten, skapat mallar och är redo att publicera appar till användarna. Men det finns fortfarande en liten detalj som behöver redas ut: licensiering. Hur fungerar det med licensiering för RemoteApp och för de appar som du delar via RemoteApp?

RemoteApp kräver varken Windows-licenser eller klientåtkomstlicenser för fjärrskrivbord. Själva RemoteApp-sidan omfattas av din prenumeration. (Läs mer om prisplanerna [här](https://azure.microsoft.com/pricing/details/remoteapp).)

Om du använder en av avbildningarna som ingår i din prenumeration kan du dela vilken som helst av de appar som är installerade på avbildningen utan att behöva en separat licens. Om du till exempel använder Windows Server 2012 R2-mallavbildningen för att skapa din samling, kan du dela System Center Endpoint Protection med dina användare. De enda undantagen till den här regeln är Office 365 ProPlus, som kräver en separat prenumeration och Office 2013, som inte kan delas i en produktionssamling.

Om du vill använda Office 365-mallavbildningen som medföljer RemoteApp, måste du ha en *aktuell* Office 365 ProPlus-prenumeration. Samma sak gäller för alla Office 365-appar som du vill publicera via en anpassad mall. Du måste aktivera apparna med din egen prenumeration. Det här gäller både för utvärderingsprenumerationer och betalda prenumerationer. Om du vill använda Office 365-mallavbildningen under utvärderingsperioden, *och du inte redan har en prenumeration*, ska du gå till Office 365-sidan och [registrera dig](https://go.microsoft.com/fwlink/p/?LinkID=403802) för en utvärderingsprenumeration. Mer info finns i [Hur fungerar RemoteApp och Office tillsammans?](remoteapp-o365.md)

Om du inte vill hämta en Office 365-utvärderingsprenumeration under utvärderingsperioden, kan du använda Office 2013 Professional Plus-mallavbildningen som ingår i RemoteApp. Den här mallavbildningen kan bara användas i 30 dagar och det går inte att konvertera den till en betald samling.

När det gäller andra appar måste du se till att du har licens att dela appen.

Det låter väl logiskt, eller hur? Du kan publicera alla appar som du har rätt att dela enligt lag. Och du måste se till att du verkligen har rätt att dela dina program.

Observera att du inte kan använda en klientåtkomstlicens eller ett volymlicensavtal i en molnsamling. Du *kan* använda ett volymlicensavtal för att aktivera program i hybridsamlingen (utom Office). Du behöver bara installera dem på mallavbildningen från volymlicensmediet. Följ informationen från programleverantören för att installera licenser i en fjärrskrivbordsmiljö.




<!--HONumber=Nov16_HO2-->


