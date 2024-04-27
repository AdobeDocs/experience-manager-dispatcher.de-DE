---
title: Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen
description: Erfahren Sie, wie Sie AEM Dispatcher konfigurieren, um CSRF-Angriffe (Cross-Site Request Forgery) zu verhindern.
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '217'
ht-degree: 46%

---

# Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein Framework, mit dem CSRF-Angriffe (Cross-Site Request Forgery) verhindert werden können. Um dieses Framework ordnungsgemäß zu verwenden, nehmen Sie die folgenden Änderungen an Ihrer Dispatcher-Konfiguration vor:

>[!NOTE]
>
>Aktualisieren Sie unbedingt die Regelnummern in den unten aufgeführten Beispielen gemäß Ihrer bestehenden Konfiguration. Beachten Sie, dass Dispatcher die letzte übereinstimmende Regel verwenden, um eine Genehmigung zu erteilen oder abzulehnen. Platzieren Sie daher die Regeln am Ende Ihrer vorhandenen Liste.

1. Im `/clientheaders` -Abschnitt Ihres `author-farm.any` und `publish-farm.any`fügen Sie am Ende der Liste den folgenden Eintrag hinzu:\
   `CSRF-Token`
1. Im Abschnitt /filters Ihrer `author-farm.any` und `publish-farm.any` oder `publish-filters.any` -Datei, fügen Sie die folgende Zeile hinzu, um Anfragen für `/libs/granite/csrf/token.json` über den Dispatcher.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Unter dem `/cache /rules` -Abschnitt Ihres `publish-farm.any`Fügen Sie eine Regel hinzu, um den Dispatcher daran zu hindern, die `token.json` -Datei. In der Regel umgehen Autoren die Zwischenspeicherung, sodass die Regel nicht zu Ihrer `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Um zu überprüfen, ob die Konfiguration korrekt ist, sehen Sie sich die Datei „dispatcher.log“ im Debugmodus an und stellen Sie sicher, dass die Datei „token.json“ weder zwischengespeichert noch von Filtern blockiert wird. Sie sollten Meldungen erhalten, die dieser ähneln:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Sie können auch überprüfen, ob Anfragen in Ihrem Apache erfolgreich sind `access_log`. Anfragen für „/libs/granite/csrf/token.json“ sollten einen HTTP-Statuscode 200 zurückgeben.
