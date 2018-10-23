---
title: Certifiera din avbildning för Azure Marketplace | Microsoft Docs
description: Beskriver hur du testar och skicka en VM-avbildning för certifiering för Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 69ebe7fde454d2f0e98371406de56f48c9ea97ae
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639940"
---
# <a name="certify-your-vm-image"></a>Certifiera din VM-avbildning

När du skapar och distribuerar den virtuella datorn (VM), måste du testa och skicka VM-avbildning för certifiering för Azure Marketplace. Den här artikeln förklarar var du kan hämta den *Test Certification Tool för Azure Certified*, hur du använder det här verktyget för att certifiera din avbildning och ladda upp verifikationsresultaten till Azure-behållaren där de virtuella hårddiskarna lagras. 


## <a name="download-and-run-the-certification-test-tool"></a>Ladda ned och kör verktyget certifiering test

Testa Certification Tool för Azure Certified körs på en lokal Windows-dator, men testar en Azure-baserade Windows eller Linux VM.  Verifierar att dina användaravbildningen är kompatibel med Microsoft Azure – att vägledningen och kraven för runt förbereder dina VHD: N har uppfyllts. Utdata från verktyget är en Kompatibilitetsrapport som du överför till den [Cloud Partner Portal](https://cloudpartner.azure.com) att begära VM-certifiering.

1. Ladda ned och installera den senaste [Test Certification Tool för Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Öppna certification tool och klicka sedan på **Start New Test**.
3. Från den **Test Information** anger en **testa namnet** för testkörningen.
4. Välj den **plattform** för den virtuella datorn antingen `Windows Server` eller `Linux`. Din plattform som används påverkar de återstående alternativen.
5. Om den virtuella datorn använder den här databastjänsten, Välj den **Test för Azure SQL Database** kryssrutan.

   ![CERT test verktyget första sidan](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Ansluta certification tool till en VM-avbildning

  Verktyget ansluter till Windows-baserade virtuella datorer med [PowerShell](https://docs.microsoft.com/powershell/) och ansluter till virtuella Linux-datorer via [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Ansluta certification tool till en Linux VM-avbildning

1. Välj den **SSH-autentisering** läge: `Password Authentication` eller `key File Authentication`.
2. Om du använder lösenordsbaserad autentisering anger du värden för den **VM DNS-namnet**, **användarnamn**, och **lösenord**.  Du kan också du kan ändra standardinställningen **SSH-porten** tal.

     ![För lösenordsautentisering av Linux VM-avbildning](./media/publishvm_026.png)

3. Om du använder key filbaserad autentisering anger du värden för den **VM DNS-namnet**, **användarnamn**, och **privata nyckeln** plats.  Du kan också ange en **lösenfras** eller ändra standarden **SSH-porten** tal.

     ![Autentisering av Linux VM-avbildning](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ansluta certification tool till en Windows-baserad VM-avbildning**
1. Ange det fullständiga **VM DNS-namnet** (till exempel `MyVMName.Cloudapp.net`).
2. Ange värden för den **användarnamn** och **lösenord**.

   ![För lösenordsautentisering av Windows VM-avbildning](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Köra ett test för certifiering

När du har angett parametervärdena för din avbildning i certification tool, väljer **Testanslutningen** att se till att en giltig anslutning till den virtuella datorn. När en anslutning har verifierats, Välj **nästa** att starta testet.  När testet är klart visas en tabell med resultaten (Pass/Fail/Warning).  I följande exempel visar resultatet av en Linux VM-test. 

![Testresultat för certifiering för Linux VM-avbildning](./media/publishvm_029.png)

Om något av testerna misslyckas, avbildningen är *inte* certifierade. Granska kraven och felmeddelanden i det här fallet, utföra de markerade ändringarna och köra testet. 

Efter automatiserad testning, du måste ange ytterligare information om VM-avbildning på den **enkät** skärmen.  Den innehåller två flikar som du måste slutföra.  Den **allmän bedömning** fliken innehåller **SANT/FALSKT** frågor, medan den **Kernel anpassning** innehåller flera val och Friform frågor.  Slutföra frågor på båda flikarna och välj sedan **nästa**.

![Certification Tool enkäten](./media/publishvm_030.png)

Den sista skärmen kan du ange ytterligare information, till exempel SSH åtkomstinformation för en Linux VM-avbildning och en förklaring till misslyckade bedömningar om du söker undantag. 

Klicka slutligen på **generera rapporten** hämta testresultat och loggfiler för utförda testfall dessutom att svaren på enkäten. Spara resultaten i samma behållare som för dina virtuella hårddiskar.

![Spara certifiering testresultat](./media/publishvm_031.png)


## <a name="next-steps"></a>Nästa steg

Du måste sedan [Generera en uniform resource Identifier (URI) för varje VHD](./cpp-get-sas-uri.md) som du skickar till marketplace. 
