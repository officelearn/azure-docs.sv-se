---
title: "Unity sammanslagning kulan självstudiekursen"
description: "Steg för att skapa klassiska kursen Unity Roll en spel som är ett krav för alla kurser i Mobile Engagement Unity"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a id="unity-roll-a-ball"></a>Skapa kursen Unity Roll en spel
Den här kursen går igenom de viktigaste stegen för en lite annorlunda [kursen Unity Roll kulan självstudiekursen](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Det här exemplet spelet består av ett sfäriskt player-objekt som kontrolleras av app-användare och syftet med spelet är ”samla in ' domänbeständiga objekt av kollision player-objektet med objekten domänbeständiga. Detta förutsätter grundläggande kunskaper med Unity Redigeraren för miljön. Om du stöter på problem ska du gå till fullständig genomgång. 

### <a name="setting-up-the-game"></a>Ställa in spelet
Stegen nedan är från den [kursen Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Öppna **Unity Editor** och på **ny**. 
   
    ![][51] 
2. Ange en **projektnamn** & **plats**väljer **3D** och på **skapa projekt**.
   
    ![][52]
3. Spara den standard scen som precis har skapat som en del av det nya projektet som med namnet **befälet** i en ny  **\_i bakgrunden** mapp under **tillgångar** mapp:
   
    ![][53]
4. Skapa en **3D-objekt -> plan** som den spela upp och Byt namn på objektet plan som **grunden**
   
    ![][1]
5. Återställa transform-komponenten för detta **grunden** objekt så att den har ursprung. 
   
    ![][3]
6. Avmarkera **Visa rutnät** från **Gizmos menyn** för den **grunden** objekt.
   
    ![][4]
7. Uppdatering av **skala** komponenten för den **grunden** objektet ska vara [X = 2, Y = 1, Z = 2]. 
   
    ![][5]
8. Lägg till en ny **3D-objekt -> sfär** till projektet och Byt namn på den här sfär objekt som **Player**. 
   
    ![][6]
9. Välj den **Player** och klicka på **Återställ omvandling** liknar plan-objektet. 
10. Uppdatera **Transform -> ställning -> Y-koordinaten** komponenten för Player Y as 0,5.  
    
    ![][7]
11. Skapa en ny mapp som kallas **material** i projekt där vi skapar material som färg Windows Media player. 
12. Skapa en ny **Material** kallas **bakgrund** i den här mappen. 
    
    ![][8]
13. Uppdatera färgen på materialet genom att uppdatera den **Albedo** -egenskapen för den. Du kan välja [0,32,64] RGB-värden. 
    
    ![][9]
14. Dra detta material till scen-vyn för att tillämpa färgen som den **grunden** objekt. 
    
    ![][10]
15. Slutligen uppdaterar den **Transform -> Rotation Y ->** till 60 på riktad enstaka objekt för tydlighetens skull. 
    
    ![][12]

### <a name="moving-the-player"></a>Flyttar Windows Media player
Stegen nedan är från den [kursen Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Lägg till en **RigidBody** komponenten till den **Player** objekt. 
   
    ![][13]
2. Skapa en ny mapp som kallas **skript** i projektet. 
3. Klicka på **Lägg till komponent -> Nytt skript -> C# skript för**. Ge den namnet **PlayerController**, och klicka på **skapa och Lägg till**. Detta skapar och bifogar ett skript till Player-objektet.  
   
    ![][14]
4. Flytta det här skriptet under den **skript** mappen i projektet. 
5. Öppna skriptet för redigering i din favorit Skriptredigeraren, uppdatera skriptkoden med följande kod och spara den. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Observera att skriptet ovan använder en **hastighet** egenskapen. Uppdatera egenskapen hastighet till 10 i Unity-redigeraren.  
   
    ![][15]
7. Träffa **spela upp** i Unity redigeraren. Du ska nu kunna styra kulan med hjälp av tangentbordet och den ska rotera och flytta runt. 

### <a name="moving-the-camera"></a>Flytta kameran
Stegen nedan är från den [kursen Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) och Binder det **Main kamera** till den **Player** objekt. 

1. Uppdatering av **Transform.Position** ska X = 0, Y = 10.5 Z-= 10.  
2. Uppdatering av **Transform.Rotation** ska X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Lägg till ett nytt skript som heter **CameraController** till den **MainCamera** och placera den under mappen skript. 
   
    ![][17]
4. Öppna skriptet för redigering och Lägg till följande kod i den:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. Miljö för Unity - dra Player-variabeln i Player fack för objektet Main kamera så att två är kopplade till varandra. 
   
    ![][18]
6. Nu om du trycka på Play i Unity-redigeraren och rotera Player kulan visas sedan kameran efter den i rörelse.  

### <a name="setting-up-the-play-area"></a>Inställningar för området Play
Stegen nedan är från den [kursen Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Vi skapar väggar omgivande grunden så att objektet Player kulan inte släppa ut området play i dess rörlighet. 

1. Klicka på **skapa -> Skapa tom spelet objekt ->** och ger den namnet **väggar**
   
    ![][19]
2. Under det här objektet väggar - skapa en ny **3D-objekt -> kuben** och ge den namnet ”Väst brandvägg”. 
   
    ![][20]
3. Uppdatering av **Transform -> ställning** och **Transform -> skala** för det här objektet i West-brandvägg. 
   
    ![][21]
4. Duplicera West-Brandvägg för att skapa en **Öst brandvägg** med den uppdaterade transformera placering och skala. 
   
    ![][22]
5. Duplicera Öst-Brandvägg för att skapa en **Nord brandvägg** med den uppdaterade transformera position & skala. 
   
    ![][23]
6. Duplicera Nord-brandvägg och skapa en **söder brandvägg** med den uppdaterade transformera position & skala. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Skapa Domänbeständiga objekt
Stegen nedan är från den [kursen Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vi skapar vissa snygg söker objekt som utgör uppsättningen domänbeständiga objekt som objektet Player kulan måste samla in av kollision med dem. 

1. Skapa en ny **3D-kub objekt** och ge den namnet hämtning. 
2. Justera det **Transform -> Rotation** & **Transform -> skala** i objektet Pickup. 
   
    ![][25]
3. Skapa och koppla en **nytt C# skript** kallas **Rotator** till objektet Pickup. Se till att placera skriptet under mappen skript. 
   
    ![][26]
4. Öppna det här skriptet för att redigera och uppdatera den om du vill ha följande: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Nu träffar rotera Play-läge för Unity-redigeraren och visa din Pickup objektet på axeln.
6. Skapa en ny mapp som kallas **Prefabs** 
   
    ![][27]
7. Dra den **hämtning** objekt och placera den i mappen Prefabs.
   
    ![][28]
8. Skapa en ny **tom spelet objektet** kallas **Upphämtningar**. Återställa dess position till ursprung och dra objektet Pickup under detta spel objekt.  
   
    ![][29]
9. Duplicera den **hämtning** objekt och sprids på den **grunden** objekt runt den **Player** objekt genom att uppdatera den **Transform.Position's X & Z** värden på lämpligt sätt. 
   
    ![][30]
10. Skapa en **nytt material** kallas **hämtning** och uppdatera den om du vill ha röd färg genom att uppdatera den **Albedo egenskapen** liknar vi gjorde för att uppdatera objektet grunden. 
    
    ![][31]
11. Använda material 4 pickup objekten.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Samla in Pickup objekt
Stegen nedan är från den [kursen Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Windows Media Player kan uppdateras så att den är ”samla in ' pickup objekt av kollision med dem. 

1. Öppna den **PlayerController** skript kopplat till Player-objektet för redigering och uppdatera den till följande:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Skapa en ny **taggen** kallas **Välj in** (den måste matcha det som finns i skriptet)  
   
    ![][33]
   
    ![][34]
3. Använd denna **taggen** till objektet Prefab hämtning. 
   
    ![][35]
4. Aktivera **IsTrigger** kryssrutan för Prefab-objektet.
   
    ![][36]
5. Lägg till en fast text till hämtning Prefab objekt. Optimera prestanda uppdateras vi statiska collider som vi använde till en dynamisk collider. 
   
    ![][37]
6. Kontrollera slutligen den **IsKinematic** -egenskapen för prefab. 
   
    ![][38]
7. Träffa **spela upp** i Unity-redigeraren och du kommer att kunna spelas upp **Återställ en boll** Spelenheter genom att flytta Player-objektet med tangenterna för riktning indata. 

### <a name="updating-the-game-for-mobile-play"></a>Uppdatera spelet för mobila play
Ovan drar slutsatsen Unity grundläggande vägledningen. Nu ska vi ändra spelet så att den mobila enheten eget. Observera att vi tangentbordsinmatning för spelet hittills för testning. Nu ändrar vi den så att vi kan styra Windows Media player genom att använda d.v.s. rörelse telefon med hjälp av accelerationsmätare som indata. 

Öppna den **PlayerController** skript för att redigera och uppdatera den **FixedUpdate** metod för att använda rörelse från accelerationsmätare för att flytta Player-objektet. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Den här självstudiekursen avslutar en grundläggande spel skapas med Unity och du kan distribuera den på en enhet som du väljer att spela. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













