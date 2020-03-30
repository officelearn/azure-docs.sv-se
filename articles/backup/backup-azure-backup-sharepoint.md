---
title: Säkerhetskopiera en SharePoint-servergrupp till Azure med DPM
description: Den här artikeln innehåller en översikt över serverskydd för DPM/Azure Backup för en SharePoint-servergrupp till Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054118"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Säkerhetskopiera en SharePoint-servergrupp till Azure med DPM

Du säkerhetskopierar en SharePoint-servergrupp till Microsoft Azure med hjälp av System Center Data Protection Manager (DPM) på ungefär samma sätt som du säkerhetskopierar andra datakällor. Azure Backup ger flexibilitet i säkerhetskopieringsschemat för att skapa dagliga, veckovisa, månatliga eller årliga säkerhetskopieringspunkter och ger dig bevarandeprincipalternativ för olika säkerhetskopieringspunkter. DPM ger möjlighet att lagra lokala diskkopior för snabba återställningstidsmål (RTO) och att lagra kopior till Azure för ekonomisk och långsiktig kvarhållning.

Säkerhetskopiering av SharePoint till Azure med DPM är en mycket liknande process som säkerhetskopierar SharePoint till DPM lokalt. Särskilda överväganden för Azure kommer att noteras i den här artikeln.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-stödda versioner och relaterade skyddsscenarier

En lista över SharePoint-versioner som stöds och DPM-versioner som krävs för att säkerhetskopiera dem finns i [Vad kan DPM säkerhetskopiera?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup)

## <a name="before-you-start"></a>Innan du börjar

Det finns några saker du måste bekräfta innan du säkerhetskopierar en SharePoint-servergrupp till Azure.

### <a name="prerequisites"></a>Krav

Innan du fortsätter kontrollerar du att du har uppfyllt alla förutsättningar för att [använda Microsoft Azure Backup för](backup-azure-dpm-introduction.md#prerequisites-and-limitations) att skydda arbetsbelastningar. Några uppgifter för förutsättningar är: skapa ett valv för säkerhetskopiering, hämta valvautentiseringsuppgifter, installera Azure Backup Agent och registrera DPM/Azure Backup Server med valvet.

Ytterligare förutsättningar och begränsningar finns i artikeln [Säkerhetskopiera SharePoint med DPM.](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Om du vill säkerhetskopiera SharePoint-servergruppen konfigurerar du skydd för SharePoint med hjälp av ConfigureSharePoint.exe och skapar sedan en skyddsgrupp i DPM. Instruktioner finns i [Konfigurera säkerhetskopiering](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) i DPM-dokumentationen.

## <a name="monitoring"></a>Övervakning

Om du vill övervaka säkerhetskopieringsjobbet följer du instruktionerna i [Övervakning dpm-säkerhetskopiering](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring)

## <a name="restore-sharepoint-data"></a>Återställa SharePoint-data

Mer information om hur du återställer ett SharePoint-objekt från en disk med DPM finns i [Återställa SharePoint-data](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med DPM

1. Om du vill återställa en SharePoint-innehållsdatabas bläddrar du igenom olika återställningspunkter (som tidigare) och väljer den återställningspunkt som du vill återställa.

    ![DPM SharePoint-skydd8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på återställningspunkten för SharePoint för att visa tillgänglig SharePoint-kataloginformation.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen är skyddad för långsiktig kvarhållning i Azure är ingen kataloginformation (metadata) tillgänglig på DPM-servern. När en sharepoint-innehållsdatabas behöver återställas måste du därför katalogisera SharePoint-servergruppen igen.
   >
   >
3. Klicka på **Omkatalog.**

    ![DPM SharePoint-skydd10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Statusfönstret **För molnrealog** öppnas.

    ![DPM SharePoint-skydd11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När katalogiseringen är klar ändras statusen till *Framgång*. Klicka på **Stäng**.

    ![DPM SharePoint-skydd12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicka på SharePoint-objektet som visas på fliken **DPM-återställning** för att hämta innehållsdatabasstrukturen. Högerklicka på objektet och klicka sedan på **Återställ**.

    ![DPM SharePoint-skydd13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Följ nu återställningsstegen tidigare i den här artikeln för att återställa en SharePoint-innehållsdatabas från disken.

## <a name="switching-the-front-end-web-server"></a>Byta frontend-webbserver

Om du har mer än en frontend-webbserver och vill byta server som DPM använder för att skydda servergruppen följer du anvisningarna i [Byta frontend-webbserver](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Nästa steg

* [Azure Backup Server och DPM – Vanliga frågor och svar](backup-azure-dpm-azure-server-faq.md)
* [Felsöka System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
