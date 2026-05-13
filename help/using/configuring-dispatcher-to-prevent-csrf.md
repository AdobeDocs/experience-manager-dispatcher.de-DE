---
title: Konfigurieren von Adobe Experience Manager Dispatcher zum Verhindern von CSRF-Angriffen
description: Erfahren Sie, wie Sie den Adobe Adobe Experience Manager Dispatcher konfigurieren, um Cross-Site Request Forgery-Angriffe zu verhindern.
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
TQID: https://experienceleague.adobe.com/xbW-j06MGU1Ku5MwXscpLdpyw8KRLE18YIz-iUgAStI
product_v2:
  - id: fd1f54a9-f50c-467d-8956-cebbaf4f3eb8
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: d095671a-1355-40aa-8b5f-06c33c68080b
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: b68483fc6956bc0e6c2b1939d2203311da62987e
workflow-type: tm+mt
source-wordcount: 236
ht-degree: 100%

---

# Konfigurieren von Adobe Experience Manager Dispatcher zum Verhindern von CSRF-Angriffen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM (Adobe Experience Manager) bietet ein Framework, mit dem Cross-Site Request Forgery-Angriffe verhindert werden können. Um dieses Framework ordnungsgemäß zu verwenden, nehmen Sie die folgenden Änderungen an Ihrer Dispatcher-Konfiguration vor:

>[!NOTE]
>
>Aktualisieren Sie unbedingt die Regelnummern in den unten aufgeführten Beispielen gemäß Ihrer bestehenden Konfiguration. Denken Sie daran, dass der Dispatcher die letzte zutreffende Regel verwendet, um eine Genehmigung zu erteilen oder zu verweigern. Platzieren Sie die Regeln daher im unteren Bereich Ihrer vorhandenen Liste.

1. Fügen Sie in den Dateien `author-farm.any` und `publish-farm.any` im Abschnitt `/clientheaders` am Ende der Liste den folgenden Eintrag hinzu:\
   `CSRF-Token`
1. Fügen Sie in den Dateien `author-farm.any` und `publish-farm.any` oder `publish-filters.any` im Abschnitt „/filter“ die folgende Zeile hinzu, um Anfragen für `/libs/granite/csrf/token.json` durch den Dispatcher zuzulassen.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`

1. Fügen Sie in der Datei `publish-farm.any` unter dem Abschnitt `/cache /rules` eine Regel hinzu, um den Dispatcher daran zu hindern, die Datei `token.json` zwischenzuspeichern. Meist umgehen Autorinnen und Autoren das Zwischenspeichern, daher müssen Sie die Regel wahrscheinlich nicht zur Datei `author-farm.any` hinzufügen.

   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Um zu überprüfen, ob die Konfiguration funktioniert, sehen Sie sich „dispatcher.log“ im DEBUG-Modus an. So können Sie sicherstellen, dass die Datei `token.json` nicht zwischengespeichert oder von Filtern blockiert wird. Sie sollten Meldungen erhalten, die dieser ähneln:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Im `access_log` von Apache kann zudem überprüft werden, ob Anfragen erfolgreich sind. Anfragen für „/libs/granite/csrf/token.json“ sollten einen HTTP-Status-Code 200 zurückgeben.
