---
title: Självstudiekurs för åtkomstkontroll & programkontroller – Azure Security Center
description: Den här självstudien visar hur du konfigurerar en just-in-time VM-åtkomstprincip och en programkontrollprincip.
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
ms.openlocfilehash: 0b28de7af16053093cd0108224188cdd615fce55
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435508"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Självstudie: Skydda dina resurser ed Azure Security Center
Security Center begränsar din exponering för hot med kontroller för åtkomst och program för att blockera skadlig aktivitet. Åtkomst till virtuella datorer (Just-in-time) minskar exponeringen för attacker genom att du kan neka beständig åtkomst till virtuella datorer. Istället tillhandahåller du kontrollerad och granskad åtkomst till virtuella datorer enbart när det behövs. Anpassningsbara programkontroller hjälper till att skydda virtuella datorer mot skadlig programvara genom att kontrollera vilka program du kan köra på dina virtuella datorer. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Konfigurera en åtkomstprincip för virtuella datorer i tid
> * Konfigurera en princip för programkontroll

## <a name="prerequisites"></a>Krav
För att gå igenom funktionerna i den här självstudien måste du ha standardnivån i Security Center. Du kan prova Security Center Standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](security-center-get-started.md) vägleder dig genom uppgraderingen till Standard.

## <a name="manage-vm-access"></a>Hantera åtkomst till virtuella datorer
JIT-åtkomst till virtuella datorer kan användas till att låsa inkommande trafik till dina virtuella Azure-datorer. Det här minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till de virtuella datorerna när du behöver.

Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När just-in-time är aktiverat använder Security Center NSG-regler (Network Security Group), som begränsar åtkomsten till hanteringsportar så att de inte kan riktas mot angripare.

1. I huvudmenyn för Säkerhetscenter väljer du **Åtkomst till Just-in-time VM** under ADVANCED CLOUD **DEFENSE**.

   ![Just-in-time-åtkomst till virtuella datorer][1]

   **Just-in-time VM-åtkomst** ger information om tillståndet för dina virtuella datorer:

   - **Konfigurerad** - virtuella datorer som har konfigurerats för att stödja ny vm-åtkomst.
   - **Rekommenderas** - virtuella datorer som kan stödja just-in-time VM-åtkomst men inte har konfigurerats till.
   - **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:

     - Saknad NSG - Just-in-time-lösningen kräver att en NSG är på plats.
     - Klassisk virtuell dator – säkerhetscenter just-in-time VM-åtkomst stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager.
     - Annat - En virtuell dator finns i den här kategorin om just-in-time-lösningen är inaktiverad i säkerhetsprincipen för prenumerationen eller resursgruppen, eller om den virtuella datorn saknar en offentlig IP och inte har en NSG på plats.

2. Välj en rekommenderad virtuell dator och klicka **på Aktivera JIT på 1 virtuell dator** för att konfigurera en just-in-time-princip för den virtuella datorn:

   Du kan spara standardportarna som Security Center rekommenderar eller så kan du lägga till och konfigurera en ny port där du vill aktivera just-in-time-lösningen. I den här självstudien ska vi lägga till en port genom att välja **Lägg till**.

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

   Avsnittet **Resursgrupper** innehåller tre flikar:

   - **Konfigurerad**: lista över resursgrupper som innehåller de virtuella datorer som konfigurerades med programkontroll.
   - **Rekommenderas**: lista över resursgrupper som programkontroll rekommenderas för.
   - **Ingen rekommendation**: lista över resursgrupper som innehåller virtuella datorer utan rekommendationer för programkontroll. Till exempel virtuella datorer där program alltid byts ut och inte har uppnått ett stabilt tillstånd.

2. Markera fliken **Rekommenderas** för att visa en lista över resursgrupper med rekommendationer för programkontroll.

   ![Rekommendationer för programkontroll][4]

3. Välj en resursgrupp för att öppna alternativet **Skapa regler för programkontroll**. I **Välj virtuella datorer** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda programkontroll för. I **Välj processer för reglerna för lista över tillåtna** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda. Listan innehåller:

   - **NAMN**: den fullständiga programsökvägen
   - **PROCESSER**: Hur många program som finns på varje sökväg
   - **VANLIGA:**"Ja" anger att dessa processer har utförts på de flesta virtuella datorer i den här resursgruppen
   - **EX EXPLOITABLE**: En varningsikon anger om programmen kan användas av en angripare för att kringgå programmets vitlistning. Vi rekommenderar att du granskar programmen innan du godkänner dem.

4. När du är färdig med dina val klickar du på **Skapa**.

## <a name="clean-up-resources"></a>Rensa resurser
De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter och självstudier fortsätter du att köra standardnivån och håller automatisk etablering aktiverad. Om du inte tänker fortsätta eller vill återgå till den kostnadsfria nivån:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj den prenumeration eller princip du vill ska återgå till den kostnadsfria nivån. **Säkerhetsprincip** öppnas.
3. Under **PRINCIPKOMPONENTER** väljer du **Prisnivå**.
4. Välj **Kostnadsfri** om du vill byta prenumeration från Standard-nivån till den kostnadsfria nivån.
5. Välj **Spara**.

Om du vill avaktivera automatisk etablering:

1. Gå tillbaka till huvudmenyn för Säkerhetscenter och välj **Säkerhetsprincipen**.
2. Välj den prenumeration du vill avaktivera automatisk etablering för.
3. Under **Säkerhetsprincip – Datainsamling** väljer du **Av** under **Registrering** för att inaktivera automatisk etablering.
4. Välj **Spara**.

>[!NOTE]
> Om du inaktiverar automatisk etablering tas inte Log Analytics-agenten bort från virtuella Azure-datorer där agenten har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att begränsa din exponering för hot genom att:

> [!div class="checklist"]
> * Konfigurera en just-in-time VM-åtkomstprincip för att ge kontrollerad och granskad åtkomst till virtuella datorer endast när det behövs
> * Konfigurera en princip för anpassningsbara programkontroller för att kontrollera vilka program som kan köras på din virtuella dator

Gå vidare till nästa kurs om du vill veta mer om att hantera säkerhetsincidenter.

> [!div class="nextstepaction"]
> [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
