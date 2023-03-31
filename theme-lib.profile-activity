<#-- custom profile activity -->
<#import "theme-lib.common-functions.ftl" as commonUtils />
<#import "theme-lib.profile-activity-macros.ftl" as messageUtils />
 
<#assign currentUser = page.context.user.id />
<#assign messages_to_show = 5 />
<#assign galleryId = settings.name.get("custom.gallery_board_id", "" )?trim />
 
<#assign baseQry = "SELECT id, subject, conversation.solved, conversation.last_post_time, conversation.last_post_time_friendly, user_context.read, board.id, board.title, board.view_href, post_time_friendly, author.login, author.view_href, author.rank.name, author.rank.color, author.rank.bold, post_time, view_href, read_only FROM messages " />
 
<#assign recentPostWhereClause = " WHERE depth=0 AND board.id!='${galleryId}' AND author.id='${currentUser}' "/>
<#assign recentCommentsWhereClause = " WHERE depth > 0 AND board.id!='${galleryId}' AND author.id='${currentUser}' "/>
<#assign recentIdeaWhereClause = " WHERE depth=0 AND conversation.style = 'idea' AND author.id='${currentUser}' " />
 
<#assign orderClause = " ORDER BY post_time DESC LIMIT " + messages_to_show />
 
<#assign recentPostQry = baseQry + recentPostWhereClause + orderClause/>
<#assign recentPostRes = commonUtils.executeLiQLQuery(recentPostQry) />
 
<#assign recentCommentsQry = baseQry + recentCommentsWhereClause + orderClause/>
<#assign recentCommentsRes = commonUtils.executeLiQLQuery(recentCommentsQry) />

<#assign recentIdeasQry = baseQry + recentIdeaWhereClause + orderClause />
<#assign recentIdeasRes = commonUtils.executeLiQLQuery(recentIdeasQry) />
 
<#assign posts_count = "SELECT count(*) FROM messages ${recentPostWhereClause}" />
<#assign messages_posts_count = commonUtils.executeLiQLQuery(posts_count, true) />
 
<#assign comments_count = "SELECT count(*) FROM messages ${recentCommentsWhereClause}" />
<#assign messages_comments_count = commonUtils.executeLiQLQuery(comments_count, true) />

<#assign recentLabelsQry = "SELECT target.id, node.id FROM subscriptions WHERE subscriber.id ='${page.context.user.id}' AND target.type = 'label'" />
<#assign recentLabelsRes = commonUtils.executeLiQLQuery(recentLabelsQry) />

<div class="custom-profile-activity">
   <section>
       <h2>${text.format("theme-lib.profile-activity.title")}</h2>
       <#-- lia tabs -->
       <div class="lia-tabs-standard-wrapper">
           <ul class="lia-tabs-standard custom-tabs" role="tablist">
               <li class="lia-tabs lia-tabs-active" id="custom-profile-activity-posts-label">
                   <span>
                       <a href="#custom-profile-activity-posts" aria-selected="true" class="lia-link-navigation tab-link" role="tab" tabindex="0">${text.format("general.Posts.activityfeed")}</a>
                   </span>
               </li>
               <li class="lia-tabs lia-tabs-inactive" id="custom-profile-activity-comments-label">
                   <span>
                       <a href="#custom-profile-activity-comments" aria-selected="false" class="lia-link-navigation tab-link" role="tab" tabindex="0">${text.format("general.Replies")}</a>
                   </span>
               </li>
               <li class="lia-tabs lia-tabs-inactive" id="custom-profile-activity-ideas-label">
                   <span>
                       <a href="#custom-profile-activity-ideas" aria-selected="false" class="lia-link-navigation tab-link" role="tab" tabindex="0">${text.format("general.Ideas")}</a>
                   </span>
               </li>
               <li class="lia-tabs lia-tabs-inactive" id="custom-profile-activity-labels-label">
                   <span>
                       <a href="#custom-profile-activity-labels" aria-selected="false" class="lia-link-navigation tab-link" role="tab" tabindex="0">${text.format("general.SubscribedLabels")}</a>
                   </span>
               </li>
           </ul>
       </div>
       <#--/lia tabs -->
       <#-- posts -->
       <section aria-labelledby="custom-profile-activity-posts-label" class="custom-tab-content" id="custom-profile-activity-posts">
           <div class="lia-user-messages custom-profile-activity-posts">
               <#if recentPostRes?size gt 0>
                   <#list recentPostRes as msg>
                       <@messageUtils.renderUserMsg msg />
                   </#list>
               <#else>
                   <div class="lia-panel-feedback-banner-note">
                       <div class="lia-text">${text.format("theme-lib.profile-activity.no-posts")}</div>
                   </div>
               </#if>
           </div>
           <#if messages_posts_count gt messages_to_show>
               <#-- lia view all -->
               <div class="lia-view-all">
                   <a class="lia-link-navigation load-more-button" href="/t5/user/viewprofilepage/user-id/${currentUser}/user-activities?messages" type="posts" max="${messages_posts_count}" target=".custom-profile-activity-posts" offset="${messages_to_show}" limit="${messages_to_show}">${text.format("li.messages.message-body.read-more")}</a>
               </div>
               <#--/lia view all -->
           </#if>
       </section>
       <#--/posts -->
       <#-- comments -->
       <section aria-labelledby="custom-profile-activity-comments-label" class="custom-tab-content" id="custom-profile-activity-comments" style="display: none;">
           <div class="lia-user-messages custom-profile-activity-comments">
               <#if recentCommentsRes?size gt 0>
                   <#list recentCommentsRes as msg>
                       <@messageUtils.renderUserMsg msg />
                   </#list>
               <#else>
                   <div class="lia-panel-feedback-banner-note">
                       <div class="lia-text">${text.format("theme-lib.profile-activity.no-replies")}</div>
                   </div>
               </#if>
           </div>
           <#if messages_comments_count gt messages_to_show>
               <#-- lia view all -->
               <div class="lia-view-all">
                   <a class="lia-link-navigation load-more-button" href="/t5/user/viewprofilepage/user-id/${currentUser}/user-activities?replies" type="comments" max="${messages_comments_count}" target=".custom-profile-activity-comments" offset="${messages_to_show}" limit="${messages_to_show}">${text.format("li.messages.message-body.read-more")}</a>
               </div>
               <#--/lia view all -->
           </#if>
       </section>
       <#--/comments -->
       <#-- ideas -->
       <section aria-labelledby="custom-profile-activity-ideas-label" class="custom-tab-content" id="custom-profile-activity-ideas" style="display: none;">
           <div class="lia-user-messages custom-profile-activity-ideas">
               <#if recentIdeasRes?size gt 0>
                   <#list recentIdeasRes as msg>
                       <@messageUtils.renderUserMsg msg />
                   </#list>
               <#else>
                   <div class="lia-panel-feedback-banner-note">
                       <div class="lia-text">${text.format("theme-lib.profile-activity.no-ideas")}</div>
                   </div>
               </#if>
           </div>
       </section>
       <#--/ideas -->
       <#-- labels -->
       <section aria-labelledby="custom-profile-activity-labels-label" class="custom-tab-content" id="custom-profile-activity-labels" style="display: none;">
           <div class="lia-user-messages custom-profile-activity-labels">
               <#if recentLabelsRes?size gt 0>
                 <#list recentLabelsRes as msg>
                       <#assign board_link = msg.node.id?remove_beginning("board:")>
                       <#assign message_name = msg.target.id?replace("%20", " ")>
                       <#assign board_name_list = board_link?split("(?=\\p{Upper})", "r")>
                       <#assign board_name = board_name_list?join(" ")> 
                       <li class="custom-profile-activity-labels-list"><a href="https://community.dynatrace.com/t5/forums/filteredbylabelpage/board-id/${"${msg.node.id}"?remove_beginning("board:")}/label-name/${msg.target.id}">${message_name}</a> in ${board_name}</li>
                  </#list>
               <#else>
                   <div class="lia-panel-feedback-banner-note">
                       <div class="lia-text">${text.format("theme-lib.profile-activity.no-labels")}</div>
                   </div>
               </#if>
           </div>
       </section>
       <#--/labels -->
   </section>
</div>

