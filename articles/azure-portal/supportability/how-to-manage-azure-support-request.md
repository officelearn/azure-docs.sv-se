---
title: Hantera en Azure-supportbegäran
description: Beskriver hur du kan visa support förfrågningar, skicka meddelanden, ändra allvarlighets grad för begäran, dela diagnostikinformation med Azure-support, öppna en stängd supportbegäran igen och ladda upp filer.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: a9dd703dc0a3f5e8f85b1022fa2a71ff9a8c295d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745578"
---
# <a name="manage-an-azure-support-request"></a>Hantera en Azure-supportbegäran

När du har [skapat en support förfrågan för Azure](how-to-create-azure-support-request.md)kan du hantera den i [Azure Portal](https://portal.azure.com), som beskrivs i den här artikeln. Du kan också skapa och hantera förfrågningar program mässigt med hjälp av [Azures support biljett REST API](/rest/api/support).

## <a name="view-support-requests"></a>Visa supportförfrågningar

Visa information och status för support förfrågningar genom att gå till **Hjälp + Support** förfrågningar  >   **alla support förfrågningar**.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Alla support förfrågningar":::

På den här sidan kan du söka efter, filtrera och sortera support förfrågningar. Välj en supportbegäran om du vill visa information, inklusive dess allvarlighets grad och eventuella meddelanden som är associerade med begäran.

## <a name="send-a-message"></a>Skicka ett meddelande

1. På sidan **alla support förfrågningar** väljer du support förfrågan.

1. På sidan **support förfrågan** väljer du **nytt meddelande**.

1. Ange ditt meddelande och välj **Skicka**.

## <a name="change-the-severity-level"></a>Ändra allvarlighets grad

> [!NOTE]
> Den högsta allvarlighets graden beror på [support avtalet](https://azure.microsoft.com/support/plans).
>

1. På sidan **alla support förfrågningar** väljer du support förfrågan.

1. På sidan **support förfrågan** väljer du **ändra**.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Ändra allvarlighets grad för support ärende":::

1. Azure Portal visar en av två skärmar, beroende på om din begäran redan har tilldelats till en support tekniker:

    - Om din begäran inte har tilldelats ser du en skärm som liknar följande. Välj en ny allvarlighets grad och välj sedan **ändra**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Välj en ny allvarlighets grad":::

    - Om din begäran har tilldelats ser du en skärm som liknar följande. Välj **OK** och skapa sedan ett [nytt meddelande](#send-a-message) för att begära en ändring av allvarlighets graden.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Det går inte att välja en ny allvarlighets grad":::

## <a name="share-diagnostic-information-with-azure-support"></a>Dela diagnostisk information med Azure-support

När du skapar en support förfrågan är som standard alternativet **dela diagnostisk information** valt. Detta gör att Azure-supporten kan samla in [diagnostikinformation](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) från dina Azure-resurser:

* Du kan inte avmarkera det här alternativet när en begäran har skapats.

* Om du har rensat alternativet när du skapar en begäran kan du välja det efter att begäran har skapats.

    1. På sidan **alla support förfrågningar** väljer du support förfrågan.
    
    1. På sidan **support förfrågan** väljer du **bevilja behörighet** och väljer sedan **Ja** och **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Bevilja behörighet för diagnostikinformation":::

## <a name="upload-files"></a>Ladda upp filer

Du kan använda fil överförings alternativet för att ladda upp diagnostikfiler eller andra filer som du tror är relevanta för en support förfrågan.

1. På sidan **alla support förfrågningar** väljer du support förfrågan.

1. På sidan **supportbegäran** bläddrar du för att hitta filen och väljer sedan **Ladda upp**. Upprepa processen om du har flera filer.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Ladda upp filen":::

### <a name="file-upload-guidelines"></a>Rikt linjer för fil uppladdning

Följ dessa rikt linjer när du använder fil överförings alternativet:

* För att skydda din integritet ska du inte inkludera någon personlig information i överföringen.
* Fil namnet får inte vara längre än 110 tecken.
* Du kan inte ladda upp fler än en fil.
* Filerna får inte vara större än 4 MB.
* Alla filer måste ha ett fil namns tillägg, till exempel *. docx* eller *. xlsx*. I följande tabell visas de fil namns tillägg som tillåts för uppladdning.

| 0-9, A-C    | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | .hwl        | . ODX  | . rar     | .tdb       | .xlam   |
| . a          | . db   | . ICS        | . oft  | . RDL     | .tdf       | .xlr    |
| . ABC        | . DMP  | . ini        | . Old  | .rdlc    | . text      | .xls    |
| . adm        | .do_  | .java       | . One  | .re_     | .thmx      | .xlsb   |
| . aspx       | .doc  | .jpg        | . OSD  | . reg     | .tif       | .xlsm   |
| .ATF        | .docm | . LDF        | . Ut  | . ta bort  | . TRC       | .xlsx   |
| . b          | .docx | . brevhuvuden | . P1   | . ren     | . TTD       | .xlt    |
| .ba_        | .dotm | . lnk        | .pcap | . Byt namn  | .tx_       | .xltx   |
| . bak        | .dotx | .lo_        | . pdb  | .rft     | .txt       | .xml    |
| .bat        | .dtsx | . log        | .pdf  | . rpt     | .uccapilog | . XMLA   |
| . blg        | . EDS  | .lpk        | .piz  | . RTE     | .uccplog   | .xps    |
| .CA_        | . EMF  | . manifest   | .pmls | .rtf     | .udcx      | . xsd    |
| . CAB        | . eml  | . Master     | .png  | . kör     | .vb_       | . xsn    |
| . Cap        | .emz  | .mdmp       | .potx | .saz     | .vbs_      | . xxx    |
| .catx       | . err  | . MOF        | .ppt  | .sql     | . vcf       | .z_     |
| . CFG        | . etl  | .mp3        | .pptm | .sqlplan | . vsd       | .z01    |
| . komprimerad | . evt  | .mpg        | .pptx | .stp     | .wdb       | .z02    |
| . Config     | . evtx | .ms_        | . prn  | .svclog  | . WKS       | . Zi     |
| .cpk        | . EX   | . msg        | . polyesterstapelfibrer  |   -       | .wma       | .zi_    |
| . cpp        | .ex_  | .msi        | . pst  |  -        | .wmv       | .zip    |
| .cs         | .ex0  | . mso        | . pub  | -         | . wmz       | .zip_   |
| . CSV        | .FRD  | . msu        | -      |-          | . WPS       | .zipp   |
| .cvr        | .gif  | . NFO        | -      |-          | .wpt       | . zippa |
| -            | . GUID | -            | -      | -         | . WSDL      | .zippy  |
| -            | . gz   | -            | -      | -         | . wsp       | .zipx   |
| -            | -      | -            | -      | -         | .wtl       | .zit    |
| -            | -      | -            | -      | -         |     -       | .zix    |
| -            | -      | -            | -      | -         |  -          | . zzz    |

## <a name="reopen-a-closed-request"></a>Öppna en stängd begäran igen

Om du behöver öppna en stängd supportbegäran igen, skapar du ett [nytt meddelande](#send-a-message)som automatiskt öppnar begäran på nytt.

## <a name="next-steps"></a>Nästa steg

[Skapa en supportbegäran för Azure](how-to-create-azure-support-request.md)

[REST-API för Azure-supportbegäranden](/rest/api/support)
