---
title: "{{ replace .Name "-" " " | title }}"
author: Me # multiple authors ["Me", "You"] 
date: "{{ .Date }}"
draft: true

showToc: true
TocOpen: false
UseHugoToc: true

comments: false
hideSummary: false

hidemeta: false
ShowReadingTime: true
ShowBreadCrumbs: true # path of the current page
ShowWordCount: false
ShowAuthor: false
ShowTranslationList: false

ShowCodeCopyButtons: true
ShowPostNavLinks: true
ShowRssButtonInSectionTermList: false

searchHidden: true
disableHLJS: true # to disable highlightjs
disableShare: true
disableHLJS: false

disableSpecial1stPost: false
disableScrollToTop: false

missingTranslationUrl: "{{ path.Dir .Path }}"

---