<script setup lang="ts">
import { GroupChannel, GroupChannelListOrder, GroupChannelListQuery, GroupChannelListQueryParams, Member, MembershipFilter, MyMemberStateFilter, SendbirdGroupChat } from '@sendbird/chat/groupChannel';
import { Ref, reactive, ref } from 'vue';
import Loader from './Loader.vue';


const props = defineProps<{
    sendbird: SendbirdGroupChat
}>()

const currentUser = props.sendbird.currentUser;
const loading: Ref = ref(true);
let groupChannels = reactive({});
const params: GroupChannelListQueryParams = {
    includeEmpty: true,
    myMemberStateFilter: MyMemberStateFilter.JOINED,
    order: GroupChannelListOrder.LATEST_LAST_MESSAGE
};
const query: GroupChannelListQuery = props.sendbird.groupChannel.createMyGroupChannelListQuery(params);
console.log(query);
if (query.hasNext) {
    query.next()
        .then((response) => {
            console.log(response);
            groupChannels = response;
            console.log(groupChannels);
            setTimeout(() => loading.value = false, 1000);
        });
}

const groupChannelName: any = (channel: GroupChannel) => {
    if (channel.isSuper) {
        return channel.name;
    }
    return channel?.members.filter((members: Member) => members?.userId != currentUser?.userId)[0].nickname;
}

const groupChannelImage: any = (channel: GroupChannel) => {
    if (channel.isSuper) {
        return channel.coverUrl;
    }
    return channel?.members.filter((members: Member) => members?.userId != currentUser?.userId)[0].profileUrl;
}
</script>

<template>
    <!-- <div class="user-list-title">Message</div> -->
    <div class="contacts-lists">
        <div v-if="loading">
            <Loader />
        </div>
        <div v-else class="chat-list">
            <div v-for="groupChannel in groupChannels" class="userchat-view">
                <div class="userchat-left flex item-center" :id="groupChannel.url">
                    <div class="user-detail-image" :data-channel_url="groupChannel.url">
                        <div class="user_avatar">
                            <img class="user_avatar-image" :height="40" :width="40" :src="groupChannelImage(groupChannel)"
                                alt="">
                            <span class="u_user-status online"></span>
                        </div>
                    </div>
                    <div class="user-detail-section">
                        <p class="user-name user_one-line">{{ groupChannelName(groupChannel) }}</p>
                        <p id="last_message" v-if="groupChannel.lastMessage">
                            <div v-if="groupChannel.lastMessage.messageType == 'user'">
                                {{ groupChannel.lastMessage.message }}
                            </div>
                            <div v-else>
                                {{ groupChannel.lastMessage.name }}
                            </div>
                        </p>
                        <p v-else>test</p>
                    </div>
                    <div class="message-time unread-message-container">
                        <p class="unread-message">{{ groupChannel.lastMessage }}</p>
                        <p v-if="groupChannel.unreadMessageCount > 0" class="unread-message-count">
                            {{ groupChannel.unreadMessageCount }}</p>
                    </div>
                </div>
                <div class="user-detail-time">
                    <p class="u_message-time u_mb-5">04:13 PM</p>
                    <div class="u_flex u_item-center u_content-end"></div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.user-list-title {
    font-size: 2rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: #fff;
    padding: 16px 20px 17px 30px;
    border-bottom: 1px solid rgba(47, 47, 47, 0.1);
    border-right: 0;
}

.contacts-lists {
    flex: 1 1;
}

.chat-list {}

.flex {
    display: flex !important;
}

.item-center {
    align-items: center;
}

.flex-rows,
.userchat-view {
    height: 100%;
    display: flex;
    align-items: center;
    flex: 1 1 auto;
}

.userchat-view {
    position: relative;
    padding: 10px 15px;
    cursor: pointer;
    align-items: flex-start;
}

.userchat-view .userchat-left {
    flex: 1 1;
}

.userchat-view .userchat-left .user-detail-image {
    position: relative;
}

.user_avatar {
    display: flex;
    cursor: pointer;
    flex-shrink: 0;
}

.user_avatar-content,
.user_avatar-image {
    width: 40px;
    height: 40px;
    border-radius: 100px;
}

.user_avatar-image {
    object-fit: contain;
    cursor: pointer;
    margin: auto;
}


.user_avatar-content {
    display: flex;
}

.user-status {
    width: 12px;
    height: 12px;
    background: #fff;
    cursor: pointer;
    border-radius: 50%;
    display: inline-block;
    position: absolute;
    right: 0;
    bottom: 0;
    background: #73788b;
    border: 1px solid #fff;
}

.user-status.online {
    background: #60b158;
    border: 1px solid #fff;
}

.userchat-view .userchat-left .user-detail-section {
    padding-left: 10px;
    flex: 1 1;
}

.user_one-line {
    -webkit-line-clamp: 1;
}

.user_one-line,
.user_two-line {
    overflow: hidden;
    display: -webkit-box;
    -webkit-box-orient: vertical;
}

.userchat-view .userchat-left .user-detail-section .user-name {
    font: normal 600 1.4rem/1.6rem "Mulish", sans-serif;
    color: #1e2538;
    text-transform: capitalize;
    word-break: break-all;
}
.userchat-view .user-detail-time {
    text-align: right;
}
</style>