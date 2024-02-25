+++
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
date = {{ .Date }}
tags = []
+++

{{< chat {{ .File.ContentBaseName }} >}}
