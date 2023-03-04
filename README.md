# Typecho-Tips-Customize-independent-page-templates-to-achieve-archiving-classification-and-labelin
typecho 主题开发过程中，某些独立页面承担着这个站点的美化或者是导航作用。  有些时候，用户自己增加的单独页面可以什么内容都没有，但是显示的时候，效果却能出来。  此时用户自己增加的单独页面就承担了导航的作用，但是里面自己写的代码又成了美化作用。  而独立页面中，文章分类、文章归档、标签、友链、关于这五个页面，前三个都是需要读取数据库，去显示内容的，这显然不能通过用户自己编辑内容去显示。  此时文章模板 能够实现这种需求
