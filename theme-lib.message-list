<#-- custom message list -->
<#import "theme-lib.common-functions.ftl" as commonUtils />
<#import "theme-lib.utility-macros.ftl" as utilities />
<#include "theme-lib.hermes-variables.ftl" />
 
<#assign showPaging = "true" />
<#assign sorting = http.request.parameters.name.get("sort", "") />
 
<#-- page size -->
<#assign pageSize = coreNode.settings.name.get("layout.messages_per_page_linear", "10")?number />
<#if user.registered>
   <#assign pageSizeUser = settings.name.get("layout.messages_per_page_linear") />
   <#if pageSizeUser?number != pageSize?number>
       <#assign pageSize = pageSizeUser />
   </#if>
</#if>
 
<#if webuisupport.path.parameters.name.get("label-name")??>
   <#assign label = webuisupport.path.parameters.name.get("label-name").getText() />
   <#assign label_query = " AND labels.text='"+label+"' " />
   <#assign msg_label_query = "" />
<#else>
   <#assign label_query = "" />
   <#assign msg_label_query = "" />
</#if>
 
<#-- grab paging param -->
<#assign pageNum = webuisupport.path.parameters.name.get("page", 1) />
 
<#assign offset = (pageNum - 1) * pageSize?number />
 
<#-- build base queries -->
<#assign baseQry = "SELECT id, subject, body, metrics.views, tags, labels, images, post_time, post_time_friendly, conversation.last_post_time, moderation_status, conversation.last_post_time_friendly, author.login, author.avatar.message, view_href, author.view_href, read_only, author.rank.name, author.rank.color, author.rank.icon_left, author.rank.bold, author.rank.icon_right, author.online_status,board.id, author.avatar.profile, board.view_href, board.title, replies.count(*), kudos.sum(weight), conversation.solved, user_context.read FROM messages WHERE depth = 0 AND board.id = '${coreNode.id}' " />
<#assign basePagingQry = "SELECT count(*) FROM messages WHERE depth = 0 AND board.id = '${coreNode.id}' " />
<#assign whereClause = label_query />
 
<#switch sorting>
   <#case "kudos">
       <#assign orderClause = "ORDER BY kudos.sum(weight) DESC " />
   <#break>
   <#case "views">
       <#assign orderClause = "ORDER BY metrics.views DESC " />
   <#break>
   <#case "replies">
       <#assign orderClause = "ORDER BY replies.count(*) DESC " />
   <#break>
   <#default>
       <#assign orderClause = "ORDER BY conversation.last_post_time DESC " />
   <#break>
</#switch>
 
<#assign limitClause = "LIMIT ${pageSize} OFFSET ${offset}" />
 
<#-- fetch recent topics by location filter -->
<#assign qry = baseQry + whereClause + orderClause + limitClause />
<#assign topics = commonUtils.executeLiQLQuery(qry) />
 
<#assign baseFloatQry = "SELECT message.id, message.tags, message.labels, message.metrics.views, message.images, message.subject, message.body, message.post_time, message.post_time_friendly, message.moderation_status, message.conversation.last_post_time, message.conversation.last_post_time_friendly, message.read_only, message.author.login, message.author.avatar.message, message.view_href, message.author.view_href, message.author.rank.name, message.author.rank.color, message.author.rank.icon_left, message.author.rank.bold, message.author.rank.icon_right, message.author.online_status,message.board.id, message.author.avatar.profile, message.board.view_href, message.board.title, message.replies.count(*), message.kudos.sum(weight), message.conversation.solved, message.user_context.read FROM floated_messages WHERE message.board.id = '${coreNode.id}'" />
 
<#assign baseUserQryScope = " AND scope = 'user' " />
<#-- get user scoped floated message -->
<#assign baseUserQry = baseFloatQry + baseUserQryScope + msg_label_query />
<#assign userFloatedTopics = commonUtils.executeLiQLQuery(baseUserQry) />
 
<#assign baseGlobalQryScope = " AND scope = 'global' " />
<#-- get global scoped floated message -->
<#assign baseGlobalQry = baseFloatQry + baseGlobalQryScope  + msg_label_query/>
<#assign globalFloatedTopics = commonUtils.executeLiQLQuery(baseGlobalQry) />
 
<div class="custom-message-list">
   <section>
       <header>
           <div class="custom-message-list-header-left">
               <select id="community-activity-sorted-by">
                   <option value="recent" <#if sorting == 'recent'>selected</#if>>${text.format("theme-lib.community-activity.recent")}</option>
                   <option value="views" <#if sorting == 'views'>selected</#if>>${text.format("theme-lib.community-activity.views")}</option>
                   <option value="replies" <#if sorting == 'replies'>selected</#if>>${text.format("theme-lib.community-activity.replies")}</option>
                   <option value="kudos" <#if sorting == 'kudos'>selected</#if>>${text.format("theme-lib.community-activity.kudos")}</option>
               </select>
               <#if page.interactionStyle=="idea">
                 <@component id="custom.idea.status.label" />
               <#else>
                 <@component id="theme-lib.start-conversation-button" />
               </#if>
           </div>
           <div class="custom-message-list-header-right">
               <#if page.interactionStyle=="idea">
                 <@component id="theme-lib.start-conversation-button" />
               <#else>
                 <@component id="custom.follow-space" />
               </#if>
           </div>
       </header>
       <#assign isOdd = true />
       <#list userFloatedTopics as userFloatedTopic>
           <#if userFloatedTopic.message??>
               <@renderMessage userFloatedTopic.message false false false false true/>
           </#if>
       </#list>
       <#list globalFloatedTopics as globalFloatedTopic>
           <#if globalFloatedTopic.message??>
               <@renderMessage globalFloatedTopic.message false false false false true/>
           </#if>
       </#list>
       <#list topics as message>
           <@renderMessage message false message?is_odd_item />
       </#list>
   </section>
</div>
<#-- pagination -->
<#if showPaging == "true">
   <#assign pagingQry = basePagingQry + whereClause />
   <#assign topicCnt = commonUtils.executeLiQLQuery(pagingQry, true) />
   <#assign pageableItem = webuisupport.paging.pageableItem.setCurrentPageNumber(pageNum).setItemsPerPage(pageSize?number).setTotalItems(topicCnt).setPagingMode("enumerated").build />
   <@component id="common.widget.pager" pageableItem=pageableItem />
</#if>
<#--/pagination -->
 
<@liaAddScript>
;(function ($) {
   $('#community-activity-sorted-by').change(function() {
       window.location = "${coreNode.webUi.url}?&sort="+$(this).val();
   });
})(LITHIUM.jQuery);
</@liaAddScript>
<#--/custom message list -->
 
<#-- render message tile in message list -->
<#macro renderMessage msg showBoard=true isOdd=false isFirst=false isLast=false isFloated=false>
   <#assign solved = "" />
   <#assign unread = "" />
   <#assign floated = "" />
   <#assign escalated = "" />
   <#assign locked = "" />
   <#assign msg_status_icon = "" />
   <#assign msg_status_txt = "" />
   <#if msg.read_only == true>
       <#assign locked = "custom-thread-locked" />
       <#assign msg_status_icon = "custom-thread-locked" />
       <#assign msg_status_txt = "theme-lib.general.thread-locked" />
   </#if>
   <#assign requires_moderation = "" />
   <#if msg.moderation_status == "unmoderated">
       <#assign requires_moderation = "custom-thread-requires-moderation" />
   </#if>
   <#if isFloated>
       <#assign floated = "custom-thread-floated" />
       <#assign msg_status_icon = "custom-thread-floated" />
       <#assign msg_status_txt = "theme-lib.general.thread-floated" />
   </#if>
   <#if msg.conversation.solved>
       <#assign solved = "custom-thread-solved" />
       <#assign msg_status_icon = "custom-thread-solved" />
       <#assign msg_status_txt = "theme-lib.general.thread-solved" />
   </#if>
   <#if !msg.user_context.read>
       <#assign unread = "custom-thread-unread" />
   </#if>
   <#assign userData  = (msg.author)!"" />
   <#assign username = (userData.login)!"" />
   <#assign id = msg.id />
   <#assign qry = "SELECT id, text, href FROM labels WHERE messages.id = '${id}' LIMIT 3" />
   <#assign labels = commonUtils.executeLiQLQuery(qry)![] />

   <#-- custom message tile -->
   <article class="custom-message-tile ${escalated} ${locked} ${solved} ${floated} ${requires_moderation} ${unread}">
       <#-- batch processing -->
       <#local showBatchProcessingCheckBox = "false"/>
       <#if user.registered>
           <#local showBatchProcessingCheckBox = settings.name.get("layout.show_batch_checkboxes")/>
       </#if>
       <#if coreNode.permissions.hasPermission("manage_messages") && showBatchProcessingCheckBox?boolean>
           <#local messageInBoard = rest("/messages/id/${msg.id}/board_id").value/>
           <#local batchProcessingValue = "${messageInBoard}:${msg.board.id}"?url/>
           <input name="bm" type="checkbox" value="${batchProcessingValue}" title="Select ${msg.subject!''}" class="BatchProcessing" />
       </#if>
       <#--/batch processing -->
       <div class="custom-message-tile-container">
        <div class="custom-user-avatar">
            <a class="UserAvatar lia-link-navigation" href="${(userData.view_href)!""}" title="${text.format('theme-lib.general.view-profile')}">
                <img class="lia-user-avatar-message" alt="${username}" src="${(userData.avatar.profile)!''}" />
            </a>
        </div>
        <div class="custom-message-tile-inner">
        <div class="custom-message-header">
            <h3>
                <#if msg.conversation.solved>
                    <i class="${solved}"><small>${text.format('theme-lib.general.thread-solved')}!</small></i>
                </#if>
                <a href="${msg.view_href}" title="${msg.subject}">
                    <#--${utils.html.truncate(140, msg.subject, '...')}-->
                    ${(msg.subject)!''}
                </a>
            </h3>
            <@utilities.messageStatistics msg/>
        </div>
      <aside>
        <div class="lia-info-area">
          <#assign reply_query = "SELECT author,view_href,id FROM messages WHERE parent.id = '${msg.id}' and author.id!='-1' ORDER BY post_time DESC LIMIT 1" />
          <#assign reply_result = commonUtils.executeLiQLQuery(reply_query) />
          <div>
            ${text.format("general.by")}
            <strong>
              <a class="lia-user" href="${(msg.author.view_href)!'#'}" rel="author" title="${text.format('theme-lib.general.view-profile')}">
                ${(msg.author.login)!"anonymous"}
              </a>
            </strong>
            <time>
              <#if msg.post_time_friendly??>
                ${msg.post_time_friendly}
              <#else>
                ${msg.post_time?string(globalDateTimeFormat)}
              </#if>
            </time>
            ${text.format("general.in")}
            <strong>
              <a href="${msg.board.view_href}">
                ${msg.board.title}
              </a>
            </strong>
            <#if reply_result?size gt 0>
              &bull;
              <a href="${reply_result[0].view_href}" title="${text.format('theme-lib.general.view-post')}">
                ${text.format("theme-lib.general.latest-reply")}
              </a>
              ${text.format("general.by")}
              <strong>
                <a class="lia-user" href="${reply_result[0].author.view_href}" rel="author" title="${text.format('theme-lib.general.view-profile')}">
                  ${reply_result[0].author.login}
                </a>
              </strong>
              <time>
                <#if msg.conversation.last_post_time_friendly??>
                  ${msg.conversation.last_post_time_friendly}
                <#else>
                  ${msg.conversation.last_post_time?string(globalDateTimeFormat)}
                </#if>
              </time>
            </#if>
          </div>
        </div>
      </aside>
      <#if labels?size gt 0>
        <ul role="list" class="lia-list-standard-inline">
          <#list labels as label>
          <li class="label">
              <#assign filterPageUrlPrefix = "${community.urls.tapestryPrefix}/forums/filteredbylabelpage/${msg.board.type}-id/${msg.board.id}/label-name/${label.text}" />
              <a href="${webuisupport.urls.getBuilder("${filterPageUrlPrefix}").build()}" class="label-link lia-link-navigation lia-custom-event" rel="tag">${label.text!''}</a>
          </li>
          </#list>
        </ul>
      </#if>
       <div>
            <#if isFloated>
                <i class="custom-thread-floated">${text.format("theme-lib.general.thread-floated")}</i>
            <#elseif msg.read_only == true>
                <i class="custom-thread-locked">${text.format("theme-lib.general.thread-locked")}</i>
            </#if>
        </div>
      </div>
    </div>
   </article>
   <#--/custom message tile -->
</#macro>
<#--/render message tile in message list -->
