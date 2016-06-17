#博客bug 记录

* Blog 表中，每次更新评论数目与阅读次数，博客的最后修改时间都会修改，一直bug 的原因是将update_date 设置为 auto_now = True
* 