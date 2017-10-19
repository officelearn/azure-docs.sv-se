---
title: "Ange säkerhetsprinciper i Azure Security Center | Microsoft Docs"
description: "I det här avsnittet berättar vi hur du ställer in säkerhetsprinciper i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Ange säkerhetsprinciper i Azure Security Center
I det här avsnittet får du genom instruktioner hjälp med att ställa in säkerhetsprinciper i Security Center.


## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principen i Security Center eller använda [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) för att skapa nya definitioner, definiera ytterligare principer och tilldela principer i hanteringsgrupper (som kan motsvara hela organisationen, en affärsenhet osv.) och övervaka efterlevnad av dessa principer i de här omfången.

> [!NOTE]
> Azure Policy finns i en begränsad förhandsversion. Klicka [här](https://aka.ms/getpolicy) om du vill ansluta. Om du vill ha mer information om Azure-principer kan du läsa [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) (Skapa och hantera principer för att genomdriva efterlevnad).

## <a name="how-to-change-security-policies-in-security-center"></a>Hur ändrar man säkerhetsprinciper i Security Center?
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer i Security Center. Om du vill ändra en säkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för den prenumerationen, eller hanteringsgruppen som innehåller den. Logga in på Azure Portal och följ dessa steg för att visa säkerhetsprinciper i Security Center:

1. Klicka på **Säkerhetsprincip** under **Allmänt** i instrumentpanelen i **Security Center**.
2. Markera den prenumeration som du vill aktivera en säkerhetsprincip för.

    ![Principhantering](./media/security-center-policies/security-center-policies-fig10.png)

3. I avsnittet **PRINCIPKOMPONENTER** klickar du på **Säkerhetsprincip**.

    ![Principkomponenter](./media/security-center-policies/security-center-policies-fig12.png)

4. Det här är standardprincipen som har tilldelats till Security Center via Azure Policy. Du kan ta bort objekt som finns under **PRINCIPER OCH PARAMETRAR** eller så kan du lägga till andra principdefinitioner som finns under **TILLGÄNGLIGA ALTERNATIV**. Det gör du genom att bara klicka på plustecknet bredvid definitionens namn.

    ![Principdefinitioner](./media/security-center-policies/security-center-policies-fig11.png)

5. Om du vill ha en mer detaljerad förklaring av principen klickar du på den så öppnas en annan sida med detaljer och JSON-koden som har strukturen [policy definition(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure):

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. När du är färdig med redigeringarna klickar du på **Spara**.

## <a name="see-also"></a>Se även
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md). Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md). Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
