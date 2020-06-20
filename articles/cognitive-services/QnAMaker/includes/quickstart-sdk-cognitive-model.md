---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114572"
---

## <a name="using-this-example-knowledge-base"></a>Använda det här exemplet i kunskaps basen

Kunskaps basen i den här snabb starten börjar med 2 konversationer QnA-par, detta görs i syfte att förenkla exemplet och ha mycket förutsägbara ID: n som ska användas i uppdaterings metoden, som kopplar uppföljnings frågor med frågor till nya par. Detta har planer ATS och implementerats i en speciell ordning för den här snabb starten.

Om du planerar att utveckla din kunskaps bas över tid med uppföljnings-prompter som är beroende av befintliga QnA-par kan du välja:
* För större kunskaps basfrågor hanterar du kunskaps basen i ett text redigerings-eller TSV-verktyg som stöder Automation och ersätter sedan kunskaps basen helt samtidigt med en uppdatering.
* För mindre kunskaps baser kan du hantera uppföljnings anvisningarna helt i QnA Maker Portal.

Information om de QnA-par som används i den här snabb starten:
* Typer av QnA-par – det finns två typer av QnA-par i den här kunskaps basen efter uppdateringen: ChitChat och domänbaserad information. Detta är vanligt om din kunskaps bank är kopplad till ett konversations program, till exempel en chattrobot.
* Även om kunskaps Svaren kan filtreras efter metadata eller använda Uppföljnings frågor, visas inte den här snabb starten. Leta efter dessa språk oberoende generateAnswer-exempel [här](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).
* Svars texten är markdown och kan innehålla en mängd [olika markdown](../reference-markdown-format.md) , t. ex. bilder (offentligt tillgängliga Internetbaserade avbildningar), länkar (till offentligt tillgängliga URL: er) och punkter, men den här snabb starten använder inte den sorten.
