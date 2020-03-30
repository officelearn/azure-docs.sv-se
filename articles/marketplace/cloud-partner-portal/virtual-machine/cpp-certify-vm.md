---
title: Certifiera din VM-avbildning för Azure Marketplace
description: Förklarar hur du testar och skickar in en VM-avbildning för Azure Marketplace-certifiering.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 0225069179e0d7d94a983c5161976a5c1933fac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278203"
---
# <a name="certify-your-vm-image"></a>Certifiera din VM-avbildning

När du har skapat och distribuerat den virtuella datorn (VM) måste du testa och skicka vm-avbildningen för Azure Marketplace-certifiering. I den här artikeln beskrivs var du kan hämta *certifieringstestverktyget för Azure Certified,* hur du använder det här verktyget för att certifiera din VM-avbildning och hur du överför verifieringsresultaten till Azure-behållaren där dina virtuella hårddiskar finns. 


## <a name="download-and-run-the-certification-test-tool"></a>Ladda ned och kör testverktyget för certifiering

Certifieringstestverktyget för Azure Certified körs på en lokal Windows-dator, men testar en Azure-baserad Windows eller Linux VM.  Den verifierar att din vm-avbildning för användare är kompatibel med Microsoft Azure – att vägledningen och kraven kring att förbereda din virtuella hårddisk har uppfyllts. Utdata för verktyget är en kompatibilitetsrapport som du överför till [Cloud Partner Portal](https://cloudpartner.azure.com) för att begära VM-certifiering.

1. Hämta och installera det senaste [certifieringstestverktyget för Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Öppna certifieringsverktyget och klicka sedan på **Starta nytt test**.
3. På skärmen **Testinformation** anger du ett **testnamn** för testkörningen.
4. Välj **plattformen** för din `Windows Server` virtuella `Linux`dator, antingen eller . Ditt plattformsval påverkar de återstående alternativen.
5. Om den virtuella datorn använder den här databastjänsten markerar du kryssrutan **Testa för Azure SQL Database.**

   ![Första sidan för certifikattestverktyget](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Ansluta certifieringsverktyget till en vm-avbildning

  Verktyget ansluter till Windows-baserade virtuella datorer med [PowerShell](https://docs.microsoft.com/powershell/) och ansluter till virtuella Linux-datorer via [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Anslut certifieringsverktyget till en Linux VM-avbildning

1. Välj **SSH-autentiseringsläge:** `Password Authentication` eller `key File Authentication`.
2. Om du använder lösenordsbaserad autentisering anger du värden för **VM DNS-namn,** **användarnamn**och **lösenord**.  Du kan också ändra **standardnumret för SSH-port.**

     ![Lösenordsautentisering av Linux VM-avbildning](./media/publishvm_026.png)

3. Om du använder nyckelfilbaserad autentisering anger du värden för **VM DNS-namn,** **användarnamn**och **privat nyckelplats.**  Du kan också ange en **Lösenfras** eller ändra standardnumret för **SSH-port.**

     ![Filautentisering av Linux VM-avbildning](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ansluta certifieringsverktyget till en Windows-baserad VM-avbildning**
1. Ange det fullständigt kvalificerade **VM-DNS-namnet** (till exempel `MyVMName.Cloudapp.net`).
2. Ange värden för **användarnamn** och **lösenord**.

   ![Lösenordsautentisering av Windows VM-avbildning](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Kör ett certifieringstest

När du har angett parametervärdena för vm-avbildningen i certifieringsverktyget väljer du **Testa anslutning** för att säkerställa en giltig anslutning till den virtuella datorn. När en anslutning har verifierats väljer du **Nästa** för att starta testet.  När testet är klart visas en tabell med testresultaten (Godkänd/underkänd/varning).  Följande exempel visar testresultaten för ett Linux VM-test. 

![Testresultat för Linux VM-avbildning](./media/publishvm_029.png)

Om något av testerna misslyckas är bilden *inte* certifierad. I det här fallet granskar du kraven och felmeddelandena, gör de angivna ändringarna och kör testet igen. 

Efter det automatiska testet måste du ange ytterligare information **Questionnaire** om vm-avbildningen på enkätskärmen.  Den innehåller två flikar som du måste fylla i.  Fliken **Allmän bedömning** innehåller **sanna/falska** frågor, medan **kernelanpassningen** innehåller flera urvals- och frihandsfrågor.  Fyll i frågorna på båda flikarna och välj sedan **Nästa**.

![Frågeformulär för certifieringsverktyg](./media/publishvm_030.png)

Den sista skärmen kan du ge ytterligare information, till exempel SSH-åtkomstinformation för en Linux VM-avbildning och en förklaring till eventuella misslyckade bedömningar om du söker undantag. 

Klicka slutligen på **Generera rapport** om du vill hämta testresultaten och loggfilerna för de utförda testfallen utöver dina svar på enkäten. Spara resultaten i samma behållare som din virtuella hårddisk.Save the results in the same container as your VHD(s).

![Spara certifieringstestresultat](./media/publishvm_031.png)


## <a name="next-steps"></a>Nästa steg

Därefter skapar du [en enhetlig resursidentifierare (URI) för varje virtuell hårddisk](./cpp-get-sas-uri.md) som du skickar till marknadsplatsen. 
