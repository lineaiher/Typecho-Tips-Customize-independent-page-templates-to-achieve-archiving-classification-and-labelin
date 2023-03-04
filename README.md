# Typecho-Tips-Customize-independent-page-templates-to-achieve-archiving-classification-and-labelin / Typecho技巧：自定义独立页面模板实现归档、分类、标签

## 一、需求

##### typecho 主题开发过程中，某些独立页面承担着这个站点的美化或者是导航作用。
##### 有些时候，用户自己增加的单独页面可以什么内容都没有，但是显示的时候，效果却能出来。
##### 此时用户自己增加的单独页面就承担了导航的作用，但是里面自己写的代码又成了美化作用。
##### 而独立页面中，文章分类、文章归档、标签、友链、关于这五个页面，前三个都是需要读取数据库，去显示内容的，这显然不能通过用户自己编辑内容去显示。
##### 此时文章模板 能够实现这种需求

## 二、开发独立页面模板
1. 基本要求
##### 在模板文件夹新建一个 php 文件，如 page-category.php（文件名不做要求，但是我习惯使用 page- 这样的前缀标明）
##### 独立页面模板文件是需要有一个固定格式的 注释头部 的，这和 index.php 的固定头部要求是类似的
##### @package custom 是固定的内容，必须存在，前面 文章分类模板 是在后台编辑的时候，选择模板列表的名字。
   



    **
     * 文章分类模板
     *
     * @package custom
     */
     
2. 显示本单页的标题
##### 如果要显示一个单页的标题，则使用下面的代码即可：
    <?php $this->title() ?>
    
3. 是否需要显示用户编辑的页面内容
##### 用户在创建单独页面的时候，是能够编辑单独页面的内容，但是是否需要显示，则根据模板的需要，如果需要显示，则需要下面的语句：
    <?php $this->content(); ?>
##### 如果不需要显示用户编辑的内容，则直接去掉这个方法的调用即可。

## 三、一些使用的模板
##### 一般来说，使用自定义模板都是实现特定的功能，比如上面提到的 文章分类、文章归档、标签等。
##### 1. 文章分类
    <?php
    /**
     * 文章分类
     *
     * @package custom
     */
    if (!defined('__TYPECHO_ROOT_DIR__')) exit; ?>
    <?php $this->need('header.php'); ?>
    <div class="col-md-12 text-center">
        <div class="page-header">
            <h2 class="page-title"><?php $this->title() ?></h2>
            <hr>
        </div>
    </div>
    <div class="col-md-12 ">
        <div class="page-wrapper">
            <div class="categary-wrapper">
                <div class="categary-content" itemprop="articleBody">
                    <div class="categary-list">
                        <?php $this->widget('Widget_Metas_Category_List')->parse('<div class="col-md-3 categary-item-wrapper"><div class="categary-item" ><a class="categary-item-link" href="{permalink}"> {name} ·  ({count})</a></div></div>'); ?>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <?php $this->need('footer.php'); ?>
    
##### 2、文章归档

    <?php
    /**
     * 文章存档
     *
     * @package custom
     */
    if (!defined('__TYPECHO_ROOT_DIR__')) exit; ?>
    <?php $this->need('header.php'); ?>
        <div class="col-md-12 text-center">
            <div class="page-header">
                <h2 class="page-title"><?php $this->title() ?></h2>
                <hr>
            </div>
        </div>
        <div class="col-md-12">
            <article class="page-wrapper" itemscope itemtype="http://schema.org/BlogPosting">
                <div class="post-content" itemprop="articleBody">
                    <?php
                    $stat = Typecho_Widget::widget('Widget_Stat');
                    Typecho_Widget::widget('Widget_Contents_Post_Recent', 'pageSize='.$stat->publishedPostsNum)->to($archives);
                    $year=0; $mon=0; $i=0; $j=0;
                    $output = '<div class="archives">';
                    while($archives->next()){
                        $year_tmp = date('Y',$archives->created);
                        $mon_tmp = date('m',$archives->created);
                        $y=$year; $m=$mon;
                        if ($year > $year_tmp || $mon > $mon_tmp) {
                            $output .= '</ul></div>';
                        }
                        if ($year != $year_tmp || $mon != $mon_tmp) {
                            $year = $year_tmp;
                            $mon = $mon_tmp;
                            $output .= '<div class="archives-item"><h4>'.date('Y年m月',$archives->created).'</h4><hr><ul class="archives_list">'; //输出年份
                        }
                        $output .= '<li>'.date('d日',$archives->created).' <a href="'.$archives->permalink .'">'. $archives->title .'</a></li>'; //输出文章
                    }
                    $output .= '</ul></div></div>';
                    echo $output;
                    ?>
                </div>
            </article>
        </div><!-- end #main-->
    <?php $this->need('footer.php'); ?>
    
##### 3、标签

    <?php
    /**
     * 标签模板
     *
     * @package custom
     */
    if (!defined('__TYPECHO_ROOT_DIR__')) exit; ?>
    <?php $this->need('header.php'); ?>
    <div class="col-md-12 text-center">
        <div class="page-header">
            <h2 class="page-title"><?php $this->title() ?></h2>
            <hr>
        </div>
    </div>
    <div class="col-md-12">
        <div class="page-wrapper">
            <div class="tags-wrapper" >
                <?php $this->widget('Widget_Metas_Tag_Cloud', 'sort=mid&ignoreZeroCount=1&desc=0')->to($tags); ?>
                <?php if($tags->have()): ?>
                <div class="tags-list">
                    <?php while ($tags->next()): ?>
                        <div class="col-md-2 tag-item"><a href="<?php $tags->permalink(); ?>" rel="tag" class="size-<?php $tags->split(5, 10, 20, 30); ?>" title="<?php $tags->count(); ?> 个话题"><?php $tags->name(); ?> (<?php $tags->count(); ?>)</a></div>
                    <?php endwhile; ?>
                    <?php else: ?>
                        <div><?php _e('没有任何标签'); ?></div>
                    <?php endif; ?>
                </div>
            </div>
        </div><!-- end #main-->
    </div>
    <?php $this->need('footer.php'); ?>
