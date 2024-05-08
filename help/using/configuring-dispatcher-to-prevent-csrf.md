---
title: Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen
description: Erfahren Sie, wie Sie AEM Dispatcher konfigurieren, um CSRF-Angriffe (Cross-Site Request Forgery) zu verhindern.
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: ht
source-wordcount: '217'
ht-degree: 100%

---

# Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein Framework, mit dem CSRF-Angriffe (Cross-Site Request Forgery) verhindert werden können. Um dieses Framework ordnungsgemäß zu verwenden, nehmen Sie die folgenden Änderungen an Ihrer Dispatcher-Konfiguration vor:

>[!NOTE]
>
>Aktualisieren Sie unbedingt die Regelnummern in den unten aufgeführten Beispielen gemäß Ihrer bestehenden Konfiguration. Denken Sie daran, dass Dispatcher die letzte zutreffende Regel verwenden, um eine Genehmigung zu erteilen oder zu verweigern. Platzieren Sie die Regeln daher im unteren Bereich Ihrer vorhandenen Liste.

1. Fügen Sie in den Dateien `author-farm.any` und `publish-farm.any` im Abschnitt `/clientheaders` am Ende der Liste den folgenden Eintrag hinzu:\
   `CSRF-Token`
1. Fügen Sie in den Dateien `author-farm.any` und `publish-farm.any` oder `publish-filters.any` im Abschnitt „/filter“ die folgende Zeile hinzu, um Anfragen für `/libs/granite/csrf/token.json` durch den Dispatcher zuzulassen.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Fügen Sie in der Datei `publish-farm.any` unter dem Abschnitt `/cache /rules` eine Regel hinzu, um den Dispatcher daran zu hindern, die Datei `token.json` zwischenzuspeichern. Meist umgehen Autorinnen und Autoren das Caching. In `author-farm.any` muss die Regel somit nicht eingefügt werden.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Um zu überprüfen, ob die Konfiguration korrekt ist, sehen Sie sich die Datei „dispatcher.log“ im Debugmodus an und stellen Sie sicher, dass die Datei „token.json“ weder zwischengespeichert noch von Filtern blockiert wird. Sie sollten Meldungen erhalten, die dieser ähneln:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Im `access_log` von Apache kann zudem überprüft werden, ob Anfragen erfolgreich sind. Anfragen für „/libs/granite/csrf/token.json“ sollten einen HTTP-Status-Code 200 zurückgeben.
