---
title: Självstudie för åtkomst & program kontroller – Azure Security Center
description: I den här självstudien lär du dig hur du konfigurerar en just-in-Time-princip för VM-åtkomst och en princip för program kontroll.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 3e4404589e180be730579b8cbbfadd132502585a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529326"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Självstudie: Skydda dina resurser ed Azure Security Center
Security Center begränsar din exponering för hot med kontroller för åtkomst och program för att blockera skadlig aktivitet. Just-in-Time (JIT)-åtkomsten för virtuella datorer minskar exponeringen för attacker genom att du kan neka beständiga åtkomst till virtuella datorer. Istället tillhandahåller du kontrollerad och granskad åtkomst till virtuella datorer enbart när det behövs. Anpassningsbara programkontroller hjälper till att skydda virtuella datorer mot skadlig programvara genom att kontrollera vilka program du kan köra på dina virtuella datorer. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Konfigurera en just-in-Time-princip för VM-åtkomst
> * Konfigurera en princip för programkontroll

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom de funktioner som beskrivs i den här självstudien måste du vara på Security Center standard pris nivån. Du kan prova Security Center Standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](security-center-get-started.md) vägleder dig genom uppgraderingen till Standard.

## <a name="manage-vm-access"></a>Hantera åtkomst till virtuella datorer
JIT-åtkomst till virtuella datorer kan användas till att låsa inkommande trafik till dina virtuella Azure-datorer. Det här minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till de virtuella datorerna när du behöver.

Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När just-in-Time har Aktiver ATS använder Security Center regler för nätverks säkerhets grupper (NSG) som begränsar åtkomsten till hanterings portar så att de inte kan nås av angripare.

1. På Security Center huvud menyn väljer du **VM-åtkomst just-in-Time** under **Avancerat moln skydd**.

   ![Just-in-time-åtkomst till virtuella datorer][1]

   **Just-in-Time VM-åtkomst** innehåller information om status för dina virtuella datorer:

   - **Konfigurerade** – virtuella datorer som har kon figurer ATS för att stödja just-in-Time VM-åtkomst.
   - **Rekommenderade** – virtuella datorer som har stöd för just-in-Time VM-åtkomst men som inte har kon figurer ATS för.
   - **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:

     - Saknar NSG – just-in-Time-lösningen kräver att en NSG är på plats.
     - Klassisk VM-Security Center just-in-Time-åtkomst för virtuella datorer stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager.
     - Övrigt – en virtuell dator finns i den här kategorin om just-in-Time-lösningen är inaktive rad i säkerhets principen för prenumerationen eller resurs gruppen, eller om den virtuella datorn saknar en offentlig IP-adress och inte har en NSG på plats.

2. Välj en rekommenderad virtuell dator och klicka på **Aktivera JIT på 1 virtuell dator** för att konfigurera en just-in-Time-princip för den virtuella datorn:

   Du kan spara standard portarna som Security Center rekommenderar eller så kan du lägga till och konfigurera en ny port som du vill aktivera just-in-Time-lösningen på. I den här självstudien ska vi lägga till en port genom att välja **Lägg till**.

   ![Lägga till portkonfiguration][2]

3. Under **Lägg till portkonfiguration** identifierar du:

   - Porten
   - Protokolltypen
   - Tillåtna käll-IP-adresser – Tillåtna IP-intervall för att få åtkomst vid en godkänd förfrågan
   - Maximal begärandetid – den maximala tidsperioden som en specifik port kan öppnas under

4. Välj **OK** för att spara.

## <a name="harden-vms-against-malware"></a>Förstärka virtuella mot skadlig programvara
Anpassningsbara programkontroller hjälper dig att definiera en uppsättning program som ska tillåtas att köras på konfigurerade resursgrupper, vilket bland annat hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.

1. Gå tillbaka till Security Center-menyn. Under **Avancerat molnskydd** väljer du **Anpassningsbara programkontroller**.

   ![Anpassningsbara programkontroller][3]

   Avsnittet **resurs grupper** innehåller tre flikar:

   - **Konfigurerad**: lista över resursgrupper som innehåller de virtuella datorer som konfigurerades med programkontroll.
   - **Rekommenderas**: lista över resursgrupper som programkontroll rekommenderas för.
   - **Ingen rekommendation**: lista över resursgrupper som innehåller virtuella datorer utan rekommendationer för programkontroll. Till exempel virtuella datorer där program alltid byts ut och inte har uppnått ett stabilt tillstånd.

2. Markera fliken **Rekommenderas** för att visa en lista över resursgrupper med rekommendationer för programkontroll.

   ![Rekommendationer för programkontroll][4]

3. Välj en resursgrupp för att öppna alternativet **Skapa regler för programkontroll**. I **Välj virtuella datorer** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda programkontroll för. I **Välj processer för reglerna för lista över tillåtna** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda. Listan innehåller:

   - **NAMN**: den fullständiga programsökvägen
   - **PROCESSER**: Hur många program som finns på varje sökväg
   - **Common**: "Ja" betyder att dessa processer har körts på de flesta virtuella datorer i den här resurs gruppen
   - Kan **utnyttjas**: en varnings ikon anger om programmen skulle kunna användas av en angripare för att kringgå Application vit listning. Vi rekommenderar att du granskar programmen innan du godkänner dem.

4. När du är färdig med dina val klickar du på **Skapa**.

## <a name="clean-up-resources"></a>Rensa resurser
De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande snabb starter och självstudier kan du fortsätta att köra standard-nivån och behålla automatisk etablering aktive rad. Om du inte tänker fortsätta eller vill återgå till den kostnadsfria nivån:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj den prenumeration eller princip du vill ska återgå till den kostnadsfria nivån. **Säkerhetsprincip** öppnas.
3. Under **PRINCIPKOMPONENTER** väljer du **Prisnivå**.
4. Välj **kostnads fri** om du vill ändra prenumerationen från standard nivån till den kostnads fria nivån.
5. Välj **Spara**.

Om du vill avaktivera automatisk etablering:

1. Gå tillbaka till Security Center huvud menyn och välj **säkerhets princip**.
2. Välj den prenumeration du vill avaktivera automatisk etablering för.
3. Under **Säkerhetsprincip – Datainsamling** väljer du **Av** under **Registrering** för att inaktivera automatisk etablering.
4. Välj **Spara**.

>[!NOTE]
> Om du inaktiverar automatisk etablering tas inte Log Analytics agenten bort från virtuella Azure-datorer där agenten har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att begränsa din exponering för hot genom att:

> [!div class="checklist"]
> * Konfigurera en just-in-Time-princip för VM-åtkomst för att tillhandahålla kontrollerad och granskad åtkomst till virtuella datorer endast vid behov
> * Konfigurera en princip för anpassningsbara programkontroller för att kontrollera vilka program som kan köras på din virtuella dator

Gå vidare till nästa kurs om du vill veta mer om att hantera säkerhetsincidenter.

> [!div class="nextstepaction"]
> [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
