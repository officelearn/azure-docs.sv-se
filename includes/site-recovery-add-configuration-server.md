---
title: ta med fil
description: ta med fil
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: 7c682105113dac7c1d457489cf926210ead77993
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187057"
---
1. Kör det enhetliga installationsprogrammet.
2. Välj **Installera konfigurationsservern och processservern**i **Innan du börjar**.

    ![Innan du börjar](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. I **Third Party Software License** (Licens för programvara från tredje part) klickar du på **I Accept** (Jag accepterar) för att ladda ned och installera MySQL.

    ![Tredjepartsprogram](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. I **Registration** (Registrering) väljer du den registreringsnyckel som du hämtade från valvet.

    ![Registrering](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. I **Internet-inställningar**anger du hur providern som körs på konfigurationsservern ansluter till Azure Site Recovery via Internet. Kontrollera att du har tillåtit de webbadresser som krävs.

    - Om du vill ansluta till proxyn som för närvarande är konfigurerad på datorn väljer du **Anslut till Azure Site Recovery med hjälp av en proxyserver**.
    - Om du vill att leverantören ska ansluta direkt väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
    - Om den befintliga proxyn kräver autentisering, eller om du vill använda en anpassad proxy för Provider-anslutningen, väljer du **Anslut med anpassade proxyinställningar**och anger adress, port och autentiseringsuppgifter.
     ![Brandvägg](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

    ![Krav](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. I **Miljöinformation**väljer du Nej om du replikerar virtuella Azure Stack-datorer eller fysiska servrar. 
9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

    ![Installationsplats](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. I **Nätverksval**väljer du först det nätverkskort som den inbyggda processservern använder för identifiering och push-installation av mobilitetstjänsten på källdatorer och välj sedan det nätverkskort som Configuration Server använder för anslutning till Azure. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

    ![Val av nätverk](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. I **Sammanfattning** granskar du informationen och klickar på **Installera**. När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats.

    ![Sammanfattning](./media/site-recovery-add-configuration-server/combined-wiz10.png)

När registreringen är klar visas servern på bladet **Inställningar** > **servrar** i valvet.
