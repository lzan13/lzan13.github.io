---
title: 关于博客主题显示楼层号以及加上楼主戳
categories:
  - Develop
  - Web
tags:
  - CSS
  - Theme
  - Wordpress
  - Html
id: 144
date: 2013-02-26 22:56:23
---

从`2013/2/14`起，我的`blog`算是正式运行起来了！

网站从开始筹划到真正运行用了有半个多月吧！
本来是打算在`2013`新年开始的时候运行的，但是由于没有弄好，就搁置到大年初五，就是今年的`2/14`那天开始正式运行了，也算是赶上了个好日子吧O(∩_∩)O~

网站的主题是通过看网上的主题制作教程一点儿一点儿的写出来的，写的过程参考了自己感觉不错的网站的样式，
效果还算不错吧，不过好多功能还没有完全实现，正在努力的完善中！

从开始到现在网站显示效果也在不断地改进，在改进的过程中参考了一些网上其他牛人的帖子，这里我就记录下我的网站显示效果更新的内容中可能大家都会需要一些内容！

首先，评论楼层的以及楼主显示，参考[莫失莫忘的帖子](http://www.imyxiao.com/1591.html)这里是给主评论加上楼层显示，嵌套的评论没有添加，同时如果作者回复了游客的评论会打上标签；效果图`无`

这里只是在原来的主题中加入了几行代码而已，主要代码在function.php中：
首先查看你的主题中评论代码
```html
function mytheme_comment($comment, $args, $depth) {
     $GLOBALS['comment'] = $comment;
?>
     <li id="comment-<?php comment_ID(); ?>" <?php comment_class(); ?> id="li-comment-<?php comment_ID() ?>">
          <div id="comment-<?php comment_ID(); ?>" class="post-comment">
               <div class="comment-meta">
                    <div class="comment-author-vcard">
                         <?php
                              $avatar_size = 55;
                              if ('0' != $comment->comment_parent){
                                   $avatar_size = 35;
                              }
                              echo get_avatar($comment, $avatar_size);
                         ?>
                         <div class="fn"><?php print get_comment_author_link(); ?></div>
                         <div class="comments-time">
                              <span>留言于</span>
                              <?php print get_comment_date('正仔新纪元 Y年Fj日'); ?>&nbsp;<?php print get_comment_time(); ?>
                              <?php edit_comment_link(__('编辑'),' ','') ?>
                         </div>
                    </div><!-- .comment-author-vcard -->
                    <?php if ($comment->comment_approved == '0') : ?>
                         <p class="comment-awaiting-moderation"><?php _e('你的评论正在等待审核……先看看其他的吧！', 'ilovelove'); ?></p>
                    <?php endif; ?>
               </div><!-- .comment-meta -->
               <div class="comment-content">
                    <?php comment_text(); ?>
               </div><!-- .comment-content -->
               <!-- 回复按钮-->
               <div class="reply">
                    <?php
                         comment_reply_link(array_merge($args,
                              array('reply_text' => __('Reply', 'ilovelove'),
                              'depth' => $depth,
                              'max_depth' => $args['max_depth'])));
                    ?>
            </div><!-- .reply -->
          </div><!-- .post-comment -->
          <div class="clear-both"></div>
     <?php
}
```
这是我的主题的评论代码，因为修改过一些，可能有些不一样，但大体如此；
下面就是添加楼层显示以及楼主标签的代码
```html
function mytheme_comment($comment, $args, $depth) {
     $GLOBALS['comment'] = $comment;
     //加入楼层的代码
     global $commentcount;
    if(!$commentcount) {
          $page = get_query_var('cpage')-1;          //获取页
        $cpp=get_option('comments_per_page');     //获取每页有多少评论
        $commentcount = $cpp * $page;
    }
     global $post;
?>
     <li id="comment-<?php comment_ID(); ?>" <?php comment_class(); ?> id="li-comment-<?php comment_ID() ?>">
          <div id="comment-<?php comment_ID(); ?>" class="post-comment">
               <div class="comment-meta">
                    <div class="comment-author-vcard">
                         <?php
                              $avatar_size = 55;
                              if ('0' != $comment->comment_parent){
                                   $avatar_size = 35;
                              }
                              echo get_avatar($comment, $avatar_size);
                         ?>
                         <div class="fn"><?php print get_comment_author_link(); ?></div>
                         <div class="comments-time">
                              <span>留言于</span>
                              <?php print get_comment_date('正仔新纪元 Y年Fj日'); ?>&nbsp;<?php print get_comment_time(); ?>
                              <?php edit_comment_link(__('编辑'),' ','') ?>
                         </div>
                    </div><!-- .comment-author-vcard -->
                    <!-- 添加楼主显示，当然你也可以添加游客显示 -->
                    <div class="post_author">
                         <?php
                              if( $comment->user_id == $post->post_author ){
                                   echo '<span class="author_yes">&nbsp;</span>'; //这里到时候在css文件中定义图片
                              }else{
                                   echo '<span class="author_no">&nbsp;</span>';     //这里是非楼主情况
                              }
                         ?>
                    </div>
                    <?php if ($comment->comment_approved == '0') : ?>
                         <p class="comment-awaiting-moderation"><?php _e('你的评论正在等待审核……先看看其他的吧！', 'ilovelove'); ?></p>
                    <?php endif; ?>
               </div><!-- .comment-meta -->
               <!-- 添加楼层显示 -->
               <div class="floor">
                              <?php
                              if(!$parent_id = $comment->comment_parent){
                                        switch ($commentcount){
                                                  case 0 :echo "席梦思";++$commentcount;break;
                                                  case 1 :echo "沙发";++$commentcount;break;
                                                  case 2 :echo "板凳";++$commentcount;break;
                                                  case 3 :echo "地板";++$commentcount;break;
                                                  default:printf('#%1$s楼', ++$commentcount);
                                        }
                              }
                              ?>
                         </div>
               <div class="comment-content">
                    <?php comment_text(); ?>
               </div><!-- .comment-content -->
               <!-- 回复按钮-->
               <div class="reply">
                    <?php
                         comment_reply_link(array_merge($args,
                              array('reply_text' => __('Reply', 'ilovelove'),
                              'depth' => $depth,
                              'max_depth' => $args['max_depth'])));
                    ?>
            </div><!-- .reply -->
          </div><!-- .post-comment -->
          <div class="clear-both"></div>
     <?php
}
```

你可以直接把上边的`function mytheme_comment($comment, $args, $depth)`方法直接拷贝覆盖你的`function`中的方法，如果你不想使用上边的覆盖，那你就把上边两部分代码中的差别部分拷贝出来，添加到你自己的`mythem_comment()`方法中去；最后在`css`中定义样式；

这是定义楼主图像的css
```css
#container .post .comments_template ul li .post-comment .post_author .author_yes{
     width:60px;
     height:45px;
     background:url(./images/author_yes.png) no-repeat;
     clear:both;
     float:right;
     position:absolute;
     margin-left:80px;
}
```