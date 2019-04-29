---
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a8ea55a40f1ee4681b6aec147e02b7bce6f1d7cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616636"
---
### <a name="retrieve-output-files"></a>Hämta utdatafiler

Du kan använda Azure-portalen till att hämta de utdatafiler i MP3-format som genereras av ffmpeg-uppgifter. 

1. Klicka på **Alla tjänster** > **Lagringskonton** och sedan på namnet på lagringskontot.
2. Klicka på **Blobbar** > *Utdata*.
3. Högerklicka på en av MP3-utdatafilerna och klicka sedan på **Ladda ned**. Följ anvisningarna i webbläsaren för att öppna eller spara filen.

![Ladda ned utdatafil](./media/batch-common-tutorial-download/download.png)

Även om det inte visas i det här exemplet kan du också ladda ned filerna programmatiskt från beräkningsnoderna eller från lagringscontainern.
