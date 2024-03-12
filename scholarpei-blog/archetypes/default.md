+++
title = "default title" 
date = {{ .Date }}
draft = true
hidden = false
tag = ["tag1","tag2"]
slug = '{{ replace .File.ContentBaseName "-" " " | title }}'

+++
