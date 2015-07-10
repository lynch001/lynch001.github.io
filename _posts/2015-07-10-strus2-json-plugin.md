---

layout: post
title: struts2-json-plugin生成的json字符串中null变转为""
category: code

---

> 应业务需要，但struts-2.3.24只提供了去除null属性，但并不支持将null值转为""，只能修改两行源码.

<!-- more -->

修改位置org.apache.struts2.json.JSONWriter

{% highlight java %}

protected void value(Object object, Method method) throws JSONException {
    if (object == null) {
        // 原为this.add("null");
        this.add("\"\"");
        return;
    }
    if (this.stack.contains(object)) {
        Class clazz = object.getClass();
        if (clazz.isPrimitive() || clazz.equals(String.class)) {
            this.process(object, method);
        } else {
            if (LOG.isDebugEnabled()) {
                LOG.debug("Cyclic reference detected on " + object);
            }
            // 原为this.add("null"); 
            this.add("\"\"");
        }
        return;
    }
    this.process(object, method);
}

{% endhighlight %}
