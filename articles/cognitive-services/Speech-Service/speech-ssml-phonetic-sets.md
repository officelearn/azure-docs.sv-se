---
title: Talfonten - Taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du kartlägger taltjänstens fonetiska alfabet till det internationella fonetiska alfabetet (IPA) och när du ska använda vilken uppsättning.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675333"
---
# <a name="speech-service-phonetic-sets"></a>Fonetiska uppsättningar för taltjänst

Taltjänsten definierar fonetiska alfabet ("telefonuppsättningar" för korta), bestående av sju språk; `en-US`, `fr-FR` `de-DE`, `es-ES` `ja-JP`, `zh-CN`, `zh-TW`och . Taltjänstens telefonuppsättningar mappas vanligtvis till <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">det <span class="docon docon-navigate-external x-hidden-focus"> </span>internationella fonetiska alfabetet (IPA). </a> Telefonuppsättningar för taltjänsten används tillsammans med [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md)som en del av text-till-tal-tjänsterbjudandet. I den här artikeln får du lära dig hur dessa telefonuppsättningar mappas och när du ska använda vilken telefonuppsättning.

# <a name="en-us"></a>[sv-USA](#tab/en-US)

### <a name="english-suprasegmentals"></a>Engelska suprasegmentals

| Exempel 1 (Debut för konsonant, ordinit för vokal) | Exempel 2 (Intervocalic för konsonant, ord medial kärna för vokal) | Exempel 3 (Coda för konsonant, ordfinal för vokal) | Kommentarer |
|--|--|--|--|
| burgare /b **er 1** r - g ax r/ | falafel /f ax - l aa **1** - f ax l/ | gitarr /g ih - t aa **1** r/ | Taltjänsttelefonuppsättning satte spänning efter vokalen av den stressade stavelsen |
| olämplig /ih **2** - n aa - p ax r - t uw 1 n/ | olika /d ih - s ih **2**- m yxa - l eh 1 - r ax - t iy/ | arbetsstyrka /w er 1 r k - f ao **2** r s/ | Taltjänsttelefonuppsättning satte spänning efter vokalen av den stressade stavelsen |

### <a name="english-vowels"></a>Engelska vokaler

| `sapi` | `ipa` | Exempel 1     | Exempel 2 | Exempel 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| Iy     | `i`   | ea t **(påt)**       | f**ee**l  | vall**ey (på annat sätt)**                  |
| Ih     | `ɪ`   | **Jag**f        | f**i**ll  |                             |
| Ey     | `eɪ`  | **en**te       | g**en**te  | d**ay (på)**                     |
| Eh     | `ɛ`   | **e**mycket     | p**e**t   | m**eh** (sällsynt ord äntligen) |
| Ae     | `æ`   | **en**ctive    | c**a**t   | n**ah** (sällsynt ord äntligen) |
| aa     | `ɑ`   | **o**bstinate (bstinate) | p**o**ppy | r**ah** (sällsynt ord äntligen) |
| Ao     | `ɔ`   | **o**räckvidd    | c au se **(på)** | Ut**ah (ut ah)**                    |
| Uh     | `ʊ`   | b**oo**k      |           |                             |
| Ow     | `oʊ`  | o ld **(o**ld)       | cl o ne (på**andra)** | g**o**                      |
| uw (uw)     | `u`   | U ber **(U**ber)      | b**oo**st | t**oo (t oo)**                     |
| Ah     | `ʌ`   | **u**ncle (3)     | c**u**t   |                             |
| Ay     | `aɪ`  | **Jag**ce       | b**i**te (på)  | fl**y (fl y)**                     |
| Aw     | `aʊ`  | **ou t (ou**t)       | s ou th **(ou**th) s ou th | c**ow (ow)**                     |
| Oy     | `ɔɪ`  | **oi l (oi**l)       | j**oi**n  | t**oy (t oy)**                     |
| y uw (y uw)   | `ju`  | **Yu**ma (yu ma)      | h**u**man | f**ew (f ew)**                     |
| Ax     | `ə`   | **en**gå       | wom**a**n | är**en**                    |

### <a name="english-r-colored-vowels"></a>Engelska R-färgade vokaler

| `sapi` | `ipa` | Exempel 1    | Exempel 2      | Exempel 3  |
|--------|-------|--------------|----------------|------------|
| ih r (på)   | `ɪɹ`  | **örat**s     | t**ir**amisu   | n**öra**   |
| eh r (på väg till)   | `ɛɹ`  | **flygplan** | app**ar**ently | sc**ar**e  |
| uh r (på)   | `ʊɹ`  |              |                | c**din**e   |
| ay r (ay r)   | `aɪɹ` | **Ire**mark  | f**ir**eplace  | ch**oir (olika)**  |
| aw r (på)   | `aʊɹ` | **timme**s    | p**ower**ful   | s**vår**   |
| ao r (på andra sätt)   | `ɔɹ`  | **eller**ange   | m**eller**al      | s**åra**   |
| aa r (på nytt)   | `ɑɹ`  | ar tist **(ar**tist)   | st**ar**t      | c**ar (på)**    |
| er r (er r)   | `ɝ`   | **öra**th    | b**ir**d       | f**din**    |
| ax r   | `ɚ`   |              | alla**er**gy    | supp**er (supp er)** |

### <a name="english-semivowels"></a>Engelska Semivowels

| `sapi` | `ipa` | Exempel 1           | Exempel 2  | Exempel 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard, f**e**w   | på**i**på  |           |

### <a name="english-aspirated-oral-stops"></a>Engelska aspirerade orala stopp

| `sapi` | `ipa` | Exempel 1 | Exempel 2   | Exempel 3  |
|--------|-------|-----------|-------------|------------|
| P      | `p`   | **p**ut (ut)   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig (på)   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk (t alk)  | capi t al **(yr ut)** | sough**t (på)** |
| d      | `d`   | **d**ig (d ig)   | sprang**d**om  | ro**d**    |
| k      | `k`   | **c**ut (olika)   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Engelska Nasal stannar

| `sapi` | `ipa` | Exempel 1        | Exempel 2  | Exempel 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**på, smash   | ca**m**era | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| Ng     | `ŋ`   |                  | li**n**k   | s**ing (ing)**    |

### <a name="english-fricatives"></a>Engelska fricatives

| `sapi` | `ipa` | Exempel 1   | Exempel 2        | Exempel 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | f ork **(f**ork)    | le**f**t         | hal**f (hal f)**   |
| v      | `v`   | v alue **(på**alue)   | e**v**ent        | lo**v**e   |
| Th     | `θ`   | **th**i    | empa th y **(y)**      | mon**th (mån)**  |
| Dh     | `ð`   | **th**sv    | mo th er (mo**th**er)       | smoo**th** |
| s      | `s`   | **s**det     | ri**s**k         | fakta**s**  |
| z      | `z`   | **z**ap (på)     | bu**s**y (på)         | barn**s**   |
| Sh     | `ʃ`   | **sh** e    | abbrevia**ti**på | ru**sh (ru sh)**   |
| Zh     | `ʒ`   | **J**acques (2) | grund**s**ure     | gara**g**e |
| h      | `h`   | h elp **(h**elp)    | sv**h**ance      | a-**h**a!  |

### <a name="english-affricates"></a>Engelska affricates

| `sapi` | `ipa` | Exempel 1 | Exempel 2    | Exempel 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**i  | fu**t**ure   | atta**ch (atta ch)** |
| Jh     | `dʒ`  | **j**oy (på)   | ori**g**inal | elleren**g**e |

### <a name="english-approximants"></a>Engelska approximanter

| `sapi` | `ipa` | Exempel 1          | Exempel 2  | Exempel 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**annons  | pa**l**ess | chi**ll (chi ll)** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r (ta r)**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Franska suprasegmentals

Den tjänste- tal ringer uppsättningen sätter spänning efter vokalen av den stressade syllablen, emellertid; Telefonuppsättningen `fr-FR` för taltjänsten stöder inte IPA-undergruppen '". Om IPA-undergruppen behövs bör du använda IPA direkt.

### <a name="french-vowels"></a>Franska vokaler

| `sapi` | `ipa` | Exempel 1     | Exempel 2       | Exempel 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **en**rbre     | p**en**tte       | ir**en**   |
| aa     | `ɑ`   |               | p**â**te        | p**a**s   |
| aa ~   | `ɑ̃`  | **sv**fläkt    | svf**en**t      | t em ps **(t)** |
| Ax     | `ə`   |               | p**e**tite      | l**e**    |
| Eh     | `ɛ`   | **e**lle (0)      | p**e**rdu       | ét**ai**t |
| Eu     | `ø`   | **œu fs (œu**fs)      | cr**eu**ser     | qu**eu (qu eu)**  |
| Ey     | `e`   | ému (olika betydelser)           | crétin (på)          | ôté (svenska)       |
| eh ~   | `ɛ̃`  | im portant **(im**portant) | p ein ture **(p ein**ture)    | matta**i** |
| Iy     | `i`   | **Jag**dée      | husdjur**i**te      | är**jag**   |
| Oe     | `œ`   | **œu f (œu**f)       | p eu r **(på andra)**        |           |
| Oh     | `ɔ`   | **o**bstacle (bstacle)  | c**o**rps       |           |
| oh ~   | `ɔ̃`  | **på**ze      | r**på**deur     | b**på**   |
| Ow     | `o`   | **au**diteur (au diteur)  | b**eau kupp**    | p**ô (ô)**    |
| oe ~   | `œ̃ ` | **Fn**        | l**un**di       | br**un**  |
| uw (uw)     | `u`   | ou trage **(ou**trage)   | intr**ou**vable | **Ou**    |
| Uy     | `y`   | u ne **(et**na)       | p**u**nir p       | él**u (olika betydelser)**   |

### <a name="french-consonants"></a>Franska konsonanter

| `sapi` | `ipa` | Exempel 1   | Exempel 2     | Exempel 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête (ête)    | ha b ille (ha**b)**   | ro**b**e                         |
| d      | `d`   | **d**ire (på)    | ron**d**eur (0)   | chau**d**e                       |
| f      | `f`   | f emme **(f**emme)   | su**ff**ixe   | bo**f (ar)**                          |
| g      | `g`   | **g**auche (  | é**g**ale     | ba**gu**e                        |
| Ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)park**ing** |
| hy     | `ɥ`   | h u ile **(h u**ile)   | n**u**ire     |                                  |
| k      | `k`   | **c**arte (på ett sätt som är)   | é**c**aille   | vara**c**                          |
| l      | `l`   | **l**ong (på)    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame (  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ous (ous)    | te**n**ir     | bo**nn**e                        |
| Nj     | `ɲ`   |             |               | pei**gn**e                       |
| P      | `p`   | **p**atte (p atte)   | re**p**som     | ca**p (ca p)**                          |
| r      | `ʁ`   | **r**på     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir (s ourir)  | en**ss**ez     | pa**ss**e                        |
| Sh     | `ʃ`   | ch anter **(olika)** | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | t ête **(t**ête)    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | i**v**ange  | rê**v**e                         |
| w      | `w`   | **ou**jag     | f**ou**ine    |                                  |
| y      | `j`   | **y od (y**od)     | p**i**étiner  | Marse**ille**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| Zh     | `ʒ`   | **j**ardin (ardin)  | man**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d (quan d)**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *Endast för vissa främmande ord.*

> [!TIP]
> Telefonuppsättningen `fr-FR` Taltjänst stöder inte följande franska liasions, `n‿`, `t‿`och `z‿`. Om de behövs bör du överväga att använda IPA direkt.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Tyska suprasegmentals

| Exempel 1 (Debut för konsonant, ordinit för vokal) | Exempel 2 (Intervocalic för konsonant, ord medial kärna för vokal) | Exempel 3 (Coda för konsonant, ordfinal för vokal) | Kommentarer |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y **ow 1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | Taltjänsttelefonuppsättning satte spänning efter vokalen av den stressade stavelsen |
| Allgemeinwissen /a **2** l - g yxa - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Taltjänsttelefonuppsättning satte spänning efter vokalen av den stressade stavelsen |

### <a name="german-vowels"></a>Tyska vokaler

| `sapi` | `ipa`     | Exempel 1                             | Exempel 2     | Exempel 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| A:     | `aː`      | **En**ber                              | Maßst**a**b   | Schem**en**                         |
| a      | `a`       | **En**bfall                            | B**a**ch      | Agath**en**                         |
| Oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| Eh:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1.</sup>](#de-v-1) Fasci**ae** |
| Eh     | `ɛ`       | **ä**ndern                            | Proz e nt **(mer/** vi/ hon är på andra sätt   | Amygdal**ae**                      |
| Ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| Iy     | `iː`      | **Jag**sprang                              | abb**dvs**gt   | Relativitätstheor**dvs**            |
| Ih     | `ɪ`       | **Jag**nnung                            | s**i**ngen    | Trä**y**                          |
| Eu     | `øː`      | **Ö**sen (olika)                              | abl**ö**sten  | Malm**ö**                          |
| Ow     | `o`, `oː` | **o**hne (hne)                              | Balk**o**n    | Trept**aj**                        |
| Oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| Ey     | `e`, `eː` | **E**berhard (0)                          | abf**e**gt    | b                                  |
| uw (uw)     | `uː`      | **U**göra                               | H**u**t       | Akk**u (1)**                           |
| Uh     | `ʊ`       | **U**nterschiedes (storbritannien)                     | b u nt **(b u**nt)      |                                    |
| Ue     | `yː`      | Ü bermut **(ü**bermut)                           | pfl**ü**gt    | Män**ü**                           |
| Uy     | `ʏ`       | **ü**ppig (ü ppig)                             | S**y**stam    |                                    |

<a id="de-v-1"></a>
**1** *Endast i ord av utländskt ursprung, såsom: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Ord-intially endast i ord av utländskt ursprung såsom **En**ppointment. Stavelse-initialt i: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Tyska diftong

| `sapi` | `ipa`       | Exempel 1    | Exempel 2          | Exempel 3 |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | ei nsam **(ei**nsam)   | Unabhängigk**ei**t | Abt**ei (på andra sätt)** |
| Aw     | `au`        | **au**ßen (au ßen)    | abb**au**st        | St**au (st au)**  |
| Oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>Tyska halvröster

| `sapi` | `ipa` | Exempel 1 | Exempel 2    | Exempel 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | lås**er** |

### <a name="german-consonants"></a>Tyska konsonanter

| `sapi` | `ipa` | Exempel 1 | Exempel 2 | Exempel 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1.</sup>](#de-c-1) Pu**b (3)** |  |
| c | `ç` | **Ch**emie (olika år) | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken (svenska) | [<sup>3.</sup>](#de-c-3) Len**d**l | [<sup>4.</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff (på) | gemana**g**t | [<sup>5 5</sup>](#de-c-5) Chan**g**e |
| f | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut (olika) |  | [<sup>6.</sup>](#de-c-6) Gre**g (mycket)** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od (2) | Reakt**i**på | hu**jag** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k (flec k)** |  |
| l | `l` | **l**au (au) | ähne**l**n | zuvie**l (på** |  |
| m | `m` | **M**ut (olika) | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| Ng | `ŋ` | [<sup>7</sup>](#de-c-7)Ng uyen **(ng**uyen) | Schwa**nk (svenska)** | R**ing (R ing)** |  |
| P | `p` | P artner **(på ett**sätt) | abru**p**t | Ti**p (et)** |  |
| Pf | `pf` | Pf erd **(pf**erd) | dammen**pf**t | Till**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise (R eise) | knu**rr**t | Haa**r (på andra)** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| Sh | `ʃ` | **Sch**ule (svenska) | mi**sch**t | lappi**sch (svenska)** |  |
| t | `t` | **T**raum (t raum) | S t raße (s**t**raße) | Mu**t** |  |
| ts | `ts` | **Z**ug (ug) | Ar**z**t | Vett**z** |  |
| ch | `tʃ` | **Tsch**echien (000) | aufgepu**tsch**t | bundesdeu**tsch (bundesdeu)** |  |
| v | `v` | w inken **(svenska)** | Q**u**alle | [<sup>9.</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11 (På)</sup>](#de-c-11)`ç` | [<sup>12 år</sup>](#de-c-12) Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper (upprea) |  |  |  |
| Zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** *Endast i ord av utländskt ursprung, såsom: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *Mjuk "ch" efter "e" och "i"*<br>
<a id="de-c-3"></a>
**3** *Endast i ord av utländskt ursprung, såsom: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Endast i ord av utländsk härkomst såsom: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Endast i ord av utländsk härkomst såsom: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Ord-terminally endast i ord av utländskt ursprung som Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Endast i ord av utländsk härkomst såsom: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Endast i ord av utländsk härkomst såsom: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Endast i ord av utländskt ursprung, såsom: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *IPA `x` är en hård "ch" efter alla icke-främre vokaler (a, aa, oh, ow, uh, uw och diftong aw).*<br>
<a id="de-c-11"></a>
**11** *IPA `ç` är en mjuk "ch" efter främre vokaler (ih, iy, eh, ae, uy, ue, oe, eu även i diftonger ay, oy) och konsonanter*<br>
<a id="de-c-12"></a>
**12** *Ord-inledningsvis endast i ord av utländskt ursprung, såsom: **J**uan. Stavelse-initialt uttrycker in också något liknande: Ba**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Tyska muntliga konsonanter

| `sapi` | `ipa` | Exempel 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> Vi måste lägga till\] en [gs telefon mellan två distinkta vokaler, utom de två vokalerna är en äkta diftong. Denna muntliga konsonant är en glottal stopp, för mer information, <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">se glottal stopp <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>.

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Spanska vokaler

| `sapi` | `ipa` | Exempel 1    | Exempel 2     | Exempel 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **en**lto     | c**en**ntar    | cas**a**     |
| i      | `i`   | **Jag**bérica  | av**jag**spa    | skatt**i**     |
| e      | `e`   | **e**lefante (på samma sätt som | på**e**nto    | elefant**e** |
| o      | `o`   | **o**caso (20)    | enc**o**ntrar | ocasenc**o** |
| U      | `u`   | u sted **(sv)**    | p**u**nta     | Juanl**u (3)**   |

### <a name="spanish-consonants"></a>Spanska konsonanter

| `sapi` | `ipa`      | Exempel 1  | Exempel 2      | Exempel 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab (en) |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b (på)**     |
| ch     | `tʃ`       | ch eque **(olika)** | co**ch**e      | Marraque**ch (olika)** |
| d      | `d`        | **d**edo (edo)   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d (på väg mot den)**     |
| f      | `f`        | **f**ácil (f ácil)  | ele f ante **(ele f**ante)   | pu**f (på)**        |
| g      | `g`        | **g**anga (nde)  |                | dópin**g**     |
|        | `ɣ`        |            | en**g**ua       | tuare**g**     |
| J      | `j`        | **Jag**odo   | cal**jag**ente   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c (på)**    |
| l      | `l`        | **l**ápiz (ápiz)  | a**l**a        | corde**l**     |
| Ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**för | en**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada (på nytt)   | ce**n**a       | rató**n (på)**      |
| Nj     | `ɲ`        | ñ aña **(ña)**   | ara**ñ**azo    |                |
| P      | `p`        | **p**oca (på)   | till**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r (abri r)**      |
| Rr     | `r`        | r adio **(på**nytt)  | co**rr**e      | pu**rr (rr)**       |
| s      | `s`        | s aco **(på**nytt)   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo (1)  | en**t**ar       | disque**t**    |
| Th     | `θ`        | **z**ebra (på ett sätt)  | a**z**ul       | lápi**z (lápi z)**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u (svenska)**       |
| x      | `x`        | **j**ota (ota)   | a**j**o        | relo**j (på)**      |

> [!TIP]
> Telefonuppsättningen `es-ES` Taltjänst stöder inte följande spanska `β`IPA, , `ð`och `ɣ`. Om de behövs bör du överväga att använda IPA direkt.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Taltjänstens telefon `zh-CN` som ställts in baseras på den inbyggda telefonen <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> Pinyin-uppsättningen.</a>

### <a name="tone"></a>Lämplig ton

| Pinyin ton | `sapi` | Exempel på tecken |
|-------------|--------|-------------------|
| mā ()          | ma 1 (1)  | Det är inte så att jag                 |
| má (på)          | ma 2 (2)  | Det är inte så att jag                 |
| mķ          | ma 3 (3)  | Det är inte så att jag                 |
| mà (på)          | ma 4 (4)  | Det är inte så att jag                 |
| Ma          | ma 5 (5)  | Det är inte så att jag                 |

#### <a name="example"></a>Exempel

| Tecken | Tjänst för taligenkänning                |
|-----------|-------------------------------|
| 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン      | zu 3 - zhi 1 - guan 1 - xi 5 |
| Det är inte så att jag        | lei 3 -jin 4                 |
| 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Taltjänstens telefon `zh-TW` som är inställd på baseras på den inbyggda telefonen <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> set.

### <a name="tone"></a>Lämplig ton

| Ton för taltjänst | Bopomofo ton | Exempel (ord) | Telefoner för taltjänst | Bopomofo | Pinyin (中ン) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| det är inte så här                   | tomt         | Det är inte så att jag              | det är inte så här                   | det är inte så här       | zhēn (zhēn)        |
| det är inte så här                   | det är inte så här             | Det är inte så att jag              | det är inte så här                   | det är inte så här      | Chá (olikartade)         |
| – Det är inte så här.                   | – Det är inte så här.             | Det är inte så att jag              | – Det är inte så här.                   | – Det är inte så här.      | dķ ( d)          |
| det är inte så här                   | det är inte så här             | Det är inte så att jag              | det är inte så här                   | det är inte så här      | wàng (wàng)        |
| ˙                   | ˙             | Det är inte så att jag             | Det är inte så att               | Det är inte så att  | yķng zi    |

#### <a name="example"></a>Exempel

| Tecken | `sapi`   |
|-----------|----------|
| Det är inte så att jag         | – Det är inte så här.      |
| Det är inte så att jag        | det är inte så här   |
| Det är inte så att jag        | Det är inte så här. |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Taltjänstens telefon `ja-JP` som ställts in baseras på den inbyggda telefonen <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana-uppsättningen. <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`huvudstress |
| `+`    | `ˌ`substress  |

#### <a name="example"></a>Exempel

| Tecken | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| Det är inte så här.        | Det är inte så att jag    | goˈwseji (på)   |
| Det är inte så att jag       | ショュ'ウ?ャ | 中jojˈw中ja |
| 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン       | 中ンンン+  | sajitecika (sajitecika) |

***