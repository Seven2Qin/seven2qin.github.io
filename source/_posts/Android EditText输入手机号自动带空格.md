---
title: EditText输入手机号自动带空格 
date: 2018/5/29 11:26:25  # 文章发表时间
tags:
- Android
categories: Android # 分类
thumbnail: http://o835lhtor.bkt.clouddn.com/blog/img/2.jpg # 略缩图
---

在android开发过程中，有时候需要这样展示手机号：186 0000  0000。

- **案例**
![这里写图片描述](https://img-blog.csdn.net/20180503180703951?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NodW5obw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


### 代码块
代码块语法遵循标准markdown代码，例如：
``` java
@Override
public void onTextChanged(CharSequence charSequence, int start, int before, int count) {

        if (charSequence == null || charSequence.length() == 0) return;
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < charSequence.length(); i++) {
            if (i != 3 && i != 8 && charSequence.charAt(i) == ' ') {
                continue;
            } else {
                sb.append(charSequence.charAt(i));
                if ((sb.length() == 4 || sb.length() == 9) && sb.charAt(sb.length() - 1) != ' ') {
                    sb.insert(sb.length() - 1, ' ');
                }
            }
        }
        if (!sb.toString().equals(charSequence.toString())) {
            int index = start + 1;
            if (sb.charAt(start) == ' ') {
                if (before == 0) {
                    index++;
                } else {
                    index--;
                }
            } else {
                if (before == 1) {
                    index--;
                }
            }
            editText.setText(sb.toString());
            editText.setSelection(index);
        }

    }
```