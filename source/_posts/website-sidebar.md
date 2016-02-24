---
title: Wordpress主题侧边栏显示最新评论带头像
categories:
  - Develop
  - Web
tags:
  - PHP
  - Theme
  - Wordpress
id: 10067
date: 2013-02-28 23:56:04
---

之前一直看到别人的博客侧边栏显示最新评论而且还带有头像很羡慕，而我的呢，在昨天还是连最新品论都显示不了呢，百度找了好久，都没有实现，然后就慢慢摸索吧，反正制作主题也把wp的函数也稍微理解一点儿了，然后就结合网上各大神的奇招，以及参考主题中其他部分的代码，再加上慢慢的测试，终于把侧边栏显示最新评论加上头像弄出来了，很开心，在这里记录分享下O(∩_∩)O~
好，先看代码：
```html
<h2>最新评论</h2>
<div id="new_comments">
     <?php
          global $wpdb;
          $sql = "SELECT DISTINCT ID, post_title, post_password, comment_ID,
               comment_post_ID, comment_author, comment_date_gmt, comment_approved,
               comment_type,comment_author_url,
               SUBSTRING(comment_content,1,15) AS com_excerpt
               FROM $wpdb->comments
               LEFT OUTER JOIN $wpdb->posts ON ($wpdb->comments.comment_post_ID =
               $wpdb->posts.ID)
               WHERE comment_approved = '1' AND comment_type = '' AND
               post_password = ''
               ORDER BY comment_date_gmt DESC
               LIMIT 20";
          $comments = $wpdb->get_results($sql);
          $output = $pre_HTML;
          $output .= "<ul>";
          foreach ($comments as $comment) {
               if(strip_tags($comment->comment_author) != "正仔" ){
                    $output .= '<li>'.get_avatar($comment, 25);
                    $output .='<a href="'.get_permalink($comment->ID).'#comment-'.$comment->comment_ID . 
                         '" title="评论来自：' .$comment->post_title . '">'.strip_tags($comment->com_excerpt).'…</a></li>';
               }
          }
          $output .= "</ul>";
          $output .= $post_HTML;
          echo $output;
     ?> 
</div><!-- new_comments   end -->
```

简单的解释下主要代码的含义；`SUBSTRING(comment_content,1,15) AS com_excerpt` `LIMIT 20";`
在`$sql`语句中有两个参数要注意，上边的`15`和下边的`20`；其中`15`是显示评论内容最多为`15`个字，`20`是显示`20`条最新的评论，你可以根据自己的需要进行更改！

接着是`get_avatar($comment, 25);`方法，
这里是参考了评论页面获得评论用户头像的方法！也可以显示评论者名字的：strip_tags($comment->comment_author) （我只显示了评论者头像），

现在发现一个比较明显的bug是头像显示的都为默认头像……正在努力解决中……
解决办法是在上边的`sql`语句中添加一个属性，即用户`email`的地址列`comment_author_email`
大家可以用下边的sql语句直接替换原来的就可以了！
```php
 $sql = "SELECT DISTINCT ID, post_title, post_password, comment_ID,
               comment_post_ID, comment_author, comment_author_email, comment_date_gmt, comment_approved,
               comment_type,comment_author_url,
               SUBSTRING(comment_content,1,12) AS com_excerpt
               FROM $wpdb->comments
               LEFT OUTER JOIN $wpdb->posts ON ($wpdb->comments.comment_post_ID =
               $wpdb->posts.ID)
               WHERE comment_approved = '1' AND comment_type = '' AND
               post_password = ''
               ORDER BY comment_date_gmt DESC
               LIMIT 20";
```

之后就是获得评论的连接和评论内容：
`get_permalink($comment->ID).'#comment-'.$comment->comment_ID ` `strip_tags($comment->com_excerpt)`

还有要注意的是，最新评论只显示游客的评论，作者的评论是过滤掉的！
我是用这句`strip_tags($comment->comment_author) != "正仔"`
本来是打算参考`function中mytheme_comment()`方法中的`('0' != $comment->comment_parent)`这个判断来过滤作者的，但是貌似不起作用，我的网站只有我一个用户，就是`正仔`，我就直接用上边的那个判断方法了，其他的朋友可以试试下边的那个判断看能不能成功！

说实话，真正一出来之后，也感觉没什么东西了……
至于`css`定义就不用贴出来了吧！