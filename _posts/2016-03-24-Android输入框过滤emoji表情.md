---
layout: post
title: Android输入框过滤Emoji表情
date: 2016-03-24
categories: blog
tags: [emoji,android,过滤,输入]
description:主要是记录一下正则，省的以后又要找
---

接到产品需求，要求密码输入框不能输入emoji表情，想想也是，不然在A手机上输入表情密码，在B手机上不一定找的到同样的表情，那就开工呗

#### 开工准备
要过滤emoji表情，那当然是要先找到怎么判断内容是不是表情了，一番搜索，伟大的谷爸没有辜负我们，找到了如下正则：

```
[\ud83c\udc00-\ud83c\udfff]|[\ud83d\udc00-\ud83d\udfff]|[\u2600-\u27ff]
```

#### 开工编码
使用TextWatcher监听输入内容，当输入的内容包含emoji表情，则清除输入框，大体代码如下

```
Pattern emojiPattern = Pattern.compile("[\\ud83c\\udc00-\\ud83c\\udfff]|[\\ud83d\\udc00-\\ud83d\\udfff]|[\\u2600-\\u27ff]",
            Pattern.UNICODE_CASE | Pattern.CASE_INSENSITIVE);
editText.addTextChangedListener(new TextWatcher() {
	...
	
	public void onTextChanged(CharSequence s, int start, int before, int count) {
		Matcher matcher = emojiPattern.matcher(s);
		if (matcher.find()) {
			editText.removeTextChangedListener(this);
			editText.setText("");
			editText.addTextChangedListener(this);
		}
	}
	
	...
})
```

以上代码虽然实现需求，但是有几个问题：

1. 表情在输入后被清除，用户可以感知到输入，体验很不好，而且用户如果误输入一个表情也会导致之前的内容被清除
2. removeTextChange/addTextChange让代码变的有点丑陋

所以思考是否有更好的实现方式，结果找到了InputFilter，完美解决了这个问题，代码如下：

```
public class EmojiInputFilter implements InputFilter {

    private Pattern mEmojiPattern = Pattern.compile("[\\ud83c\\udc00-\\ud83c\\udfff]|[\\ud83d\\udc00-\\ud83d\\udfff]|[\\u2600-\\u27ff]",
            Pattern.UNICODE_CASE | Pattern.CASE_INSENSITIVE);

    @Override
    public CharSequence filter(CharSequence source, int start, int end, Spanned dest, int dstart, int dend) {
        Matcher matcher = mEmojiPattern.matcher(source);
        if (matcher.find()) {
            return "";
        }
        return null;
    }
}
```
如果输入的内容中包含了emoji表情，那么整个文本都不会被输入，从别处粘帖也不行哦





