---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127361"
---
### <a name="retrieve-output-files"></a>Hämta utdatafiler

Du kan använda Azure-portalen till att hämta de utdatafiler i MP3-format som genereras av ffmpeg-uppgifter. 

1. Klicka på **Alla tjänster** > **Lagringskonton** och sedan på namnet på lagringskontot.
2. Klicka på **Blobbar** > *Utdata*.
3. Högerklicka på en av MP3-utdatafilerna och klicka sedan på **Ladda ned**. Följ anvisningarna i webbläsaren för att öppna eller spara filen.

![Ladda ned utdatafil](./media/batch-common-tutorial-download/download.png)

Även om det inte visas i det här exemplet kan du också ladda ned filerna programmatiskt från beräkningsnoderna eller från lagringscontainern.
