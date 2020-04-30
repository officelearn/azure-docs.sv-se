---
title: Certifiera din VM-avbildning för Azure Marketplace
description: Förklarar hur du testar och skickar en VM-avbildning för Azure Marketplace-certifiering.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 81dec159a8a84adce97750666baba455ae3fdd93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147082"
---
# <a name="certify-your-vm-image"></a>Certifiera din VM-avbildning

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Azures avbildnings certifiering för virtuella datorer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) för att hantera dina migrerade erbjudanden.

När du har skapat och distribuerat den virtuella datorn (VM) måste du testa och skicka VM-avbildningen för Azure Marketplace-certifiering. I den här artikeln beskrivs var du kan hämta *certifierings test verktyget för Azure Certified*, hur du använder det här verktyget för att certifiera din VM-avbildning och hur du överför verifierings resultatet till Azure-behållaren där dina virtuella hård diskar finns. 


## <a name="download-and-run-the-certification-test-tool"></a>Hämta och kör verktyget för certifierings test

Certifierings test verktyget för Azure Certified körs på en lokal Windows-dator, men testar en Azure-baserad virtuell Windows-eller Linux-dator.  Den verifierar att den virtuella användar avbildningen är kompatibel med Microsoft Azure – att rikt linjerna och kraven kring att förbereda din virtuella hård disk har uppfyllts. Utdata från verktyget är en kompatibilitetsrapport, som du kommer att överföra till [Cloud Partner Portal](https://cloudpartner.azure.com) för att begära VM-certifiering.

1. Hämta och installera det senaste [certifierings test verktyget för Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Öppna certifierings verktyget och klicka sedan på **Starta nytt test**.
3. På skärmen **testa information** anger du ett **testnamn** för test körningen.
4. Välj **plattform** för den virtuella datorn, antingen `Windows Server` eller `Linux`. Valet av plattform påverkar de återstående alternativen.
5. Om den virtuella datorn använder den här databas tjänsten markerar du kryss rutan **test för Azure SQL Database** .

   ![Start sida för cert test verktyg](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Ansluta certifierings verktyget till en VM-avbildning

  Verktyget ansluter till Windows-baserade virtuella datorer med [PowerShell](https://docs.microsoft.com/powershell/) och ansluter till virtuella Linux-datorer via [SSH.net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Ansluta certifierings verktyget till en virtuell Linux-avbildning

1. Välj **SSH-autentiseringsläge** : `Password Authentication` eller. `key File Authentication`
2. Om du använder lösenordsbaserad autentisering anger du värden för den **virtuella datorns DNS-namn**, **användar namn**och **lösen ord**.  Alternativt kan du ändra standard-SSH- **portnumret** .

     ![Lösenordsautentisering för virtuell Linux-avbildning](./media/publishvm_026.png)

3. Om du använder nyckelbaserad autentisering anger du värden för den **virtuella datorns DNS-namn**, **användar namn**och plats för den **privata nyckeln** .  Alternativt kan du ange en **lösen fras** eller ändra standardvärdet för **SSH-porten** .

     ![Filautentisering av virtuell Linux-avbildning](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ansluta certifierings verktyget till en Windows-baserad VM-avbildning**
1. Ange det fullständigt kvalificerade **DNS-namnet för virtuell dator** ( `MyVMName.Cloudapp.net`till exempel).
2. Ange värden för **användar namn** och **lösen ord**.

   ![Lösenordsautentisering för Windows VM-avbildning](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Kör ett certifierings test

När du har angett parameter värden för din VM-avbildning i certifierings verktyget väljer du **Testa anslutning** för att säkerställa en giltig anslutning till den virtuella datorn. När en anslutning har verifierats väljer du **Nästa** för att starta testet.  När testet är klart visas en tabell med test resultaten (pass/misslyckande/varning).  I följande exempel visas test resultatet för ett test av en virtuell Linux-dator. 

![Certifierings test resultat för den virtuella Linux-avbildningen](./media/publishvm_029.png)

Om något av testerna inte fungerar är din avbildning *inte* godkänd. I det här fallet kan du granska kraven och felen, göra de angivna ändringarna och köra testet igen. 

Efter det automatiserade testet måste du ange ytterligare information om den virtuella dator avbildningen på **enkät** skärmen.  Den innehåller två flikar som du måste slutföra.  Fliken **allmän bedömning** innehåller **True/false** -frågor, medan **kernel-anpassningen** innehåller flera urvals-och fri hands frågor.  Slutför frågorna på båda flikarna och välj sedan **Nästa**.

![Certificate Tool-enkät](./media/publishvm_030.png)

På den sista skärmen kan du ange ytterligare information, till exempel SSH Access-information för en virtuell Linux-avbildning och en förklaring för eventuella misslyckade utvärderingar om du söker efter undantag. 

Klicka slutligen på **Generera rapport** för att ladda ned test resultaten och loggfilerna för de test ärenden som körts, förutom dina svar till enkäten. Spara resultatet i samma behållare som dina VHD: er.

![Spara certifierings test resultat](./media/publishvm_031.png)


## <a name="next-steps"></a>Nästa steg

Sedan kommer du att [Generera en URI (Uniform Resource Identifier) för varje virtuell hård disk](./cpp-get-sas-uri.md) som du skickar till Marketplace. 
