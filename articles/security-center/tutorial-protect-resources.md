---
title: "Azure Security Center-självstudie – Skydda dina resurser med Azure Security Center | Microsoft Docs"
description: "I den här självstudien får du se hur du konfigurerar en princip för Just-in-time-åtkomst till virtuell dator och en princip för programkontroll."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: cda204f5b54aef239cc0795b62c6fa484a27ebb5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Självstudie: Skydda dina resurser ed Azure Security Center
Security Center begränsar din exponering för hot med kontroller för åtkomst och program för att blockera skadlig aktivitet. Just-in-time-åtkomst till virtuell dator (VM) minskar din exponering för attacker genom att göra det möjligt för dig att neka beständig åtkomst till virtuella datorer. Istället tillhandahåller du kontrollerad och granskad åtkomst till virtuella datorer enbart när det behövs. Anpassningsbara programkontroller hjälper till att skydda virtuella datorer mot skadlig programvara genom att kontrollera vilka program du kan köra på dina virtuella datorer. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera just-in-time-åtkomstprincip för virtuell dator
> * Konfigurera en princip för programkontroll

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom funktionerna i den här självstudien måste du ha standardnivån i Security Center. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna. Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](security-center-get-started.md) vägleder dig genom uppgraderingen till Standard.

## <a name="manage-vm-access"></a>Hantera åtkomst till virtuella datorer
Just in time-åtkomst till virtuella datorer kan användas till att låsa inkommande trafik till dina virtuella Azure-datorer. Det här minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till virtuella datorer när du behöver.

Just-in-time-åtkomst till virtuell dator är i förhandsversion.

Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När Just-in-time är aktiverat använder Security Center NSG-regler (Network Security Group), vilket begränsar åtkomsten till hanteringsportar så de inte kan nås av angripare.

1. På huvudmenyn i Security Center väljer du **Just-in-time-åtkomst till virtuell dator** under **Avancerat molnskydd**.

  ![Just-in-time-åtkomst till virtuell dator][1]

  **Just-in-time-åtkomst till virtuell dator** tillhandahåller information om dina virtuella datorers status:

  - **Konfigurerad** – Virtuella datorer som har konfigurerats för att stödja Just-in-time-åtkomst till virtuella datorer.
  - **Rekommenderas** – Virtuella datorer som kan stödja Just-in-time-åtkomst till virtuell dator men som inte har konfigurerats för det.
  - **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:

    - Saknad NSG – Just-in-time-lösningen kräver att det finns en NSG.
    - Klassisk virtuell dator – Security Centers just-in-time-åtkomst till virtuell dator stöder för närvarande bara virtuella datorer som har distribuerats via Azure Resource Manager.
    - Övrigt – En virtuell dator i den här kategorin om just-in-time-lösningen är avstängd i säkerhetsprincipen för prenumerationen eller resursgruppen, eller om den virtuella datorn saknar en offentlig IP-adress och inte har någon NSG.

2. Välj en rekommenderad virtuell dator och klicka på alterantivet för att **aktivera JIT på 1 virtuell dator** för att konfigurera en just-in-time-princip för den virtuella datorn:

  Du kan spara standardportarna som Security Center rekommenderar eller så kan du lägga till och konfigurera en ny port som du vill aktivera just-in-time-lösningen på. I den här självstudien ska vi lägga till en port genom att välja **Lägg till**.

  ![Lägga till portkonfiguration][2]

3. Under **Lägg till portkonfiguration** identifierar du:

  - Porten
  - Protokolltypen
  - Tillåtna käll-IP-adresser – Tillåtna IP-intervall för att få åtkomst vid en godkänd förfrågan
  - Maximal begärandetid – den maximala tidsperioden som en specifik port kan öppnas under

4. Välj **OK** för att spara.

## <a name="harden-vms-against-malware"></a>Förstärka virtuella mot skadlig programvara
Anpassningsbara programkontroller hjälper dig att definiera en uppsättning program som ska tillåtas att köras på konfigurerade resursgrupper, vilket bland annat hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.

Adaptiva programkontroller är i förhandsversion. Den här funktionen är endast tillgänglig för Windows-datorer.

1. Gå tillbaka till Security Center-menyn. Under **Avancerat molnskydd** väljer du **Anpassningsbara programkontroller**.

   ![Anpassningsbara programkontroller][3]

  Avsnittet **Resursgrupper** innehåller tre flikar:

  - **Konfigurerad**: lista över resursgrupper som innehåller de virtuella datorer som konfigurerades med programkontroll.
  - **Rekommenderas**: lista över resursgrupper som programkontroll rekommenderas för.
  - **Ingen rekommendation**: lista över resursgrupper som innehåller virtuella datorer utan rekommendationer för programkontroll. Till exempel virtuella datorer där program alltid byts ut och inte har uppnått ett stabilt tillstånd.

2. Markera fliken **Rekommenderas** för att visa en lista över resursgrupper med rekommendationer för programkontroll.

  ![Rekommendationer för programkontroll][4]

3. Välj en resursgrupp för att öppna alternativet **Skapa regler för programkontroll**. I **Välj virtuella datorer** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda programkontroll för. I **Välj processer för reglerna för lista över tillåtna** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda. Listan innehåller:

  - **NAMN**: den fullständiga programsökvägen
  - **PROCESSER**: Hur många program som finns på varje sökväg
  - **GEMENSAM**: "Ja" betyder att dessa processer har körts på de flesta virtuella datorerna i den här resursgruppen
  - **EXPLOATERBAR**: En varningsikon anger om programmen skulle kunna användas av en angripare för att kringgå listan över tillåtna program. Vi rekommenderar att du granskar programmen innan du godkänner dem.

4. När du är färdig med dina val klickar du på **Skapa**.

## <a name="clean-up-resources"></a>Rensa resurser
De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du tänker fortsätta med att arbeta med efterföljande snabbstarter och självstudier ska du fortsätta att köra Standard-nivån och ha automatisk etablering aktiverad. Om du inte tänker fortsätta eller vill återgå till den kostnadsfria nivån:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj den prenumeration eller princip du vill ska återgå till den kostnadsfria nivån. **Säkerhetsprincip** öppnas.
3. Under **PRINCIPKOMPONENTER** väljer du **Prisnivå**.
4. Välj **Kostnadsfri** om du vill byta prenumeration från Standard-nivån till den kostnadsfria nivån.
5. Välj **Spara**.

Om du vill avaktivera automatisk etablering:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj den prenumeration du vill avaktivera automatisk etablering för.
3. Under **Säkerhetsprincip – Datainsamling** väljer du **Av** under **Registrering** för att inaktivera automatisk etablering.
4. Välj **Spara**.

>[!NOTE]
> Inaktivering av automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att begränsa din exponering för hot genom att:

> [!div class="checklist"]
> * Konfigurera en princip för Just-in-time-åtkomst till virtuell dator för att tillhandahålla kontrollerad och granskad åtkomst till virtuella datorer enbart när det behövs
> * Konfigurera en princip för anpassningsbara programkontroller för att kontrollera vilka program som kan köras på din virtuella dator

Gå vidare till nästa kurs om du vill veta mer om att hantera säkerhetsincidenter.

> [!div class="nextstepaction"]
> [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
