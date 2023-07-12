<script setup lang="ts">
import { DefineComponent, Ref, defineAsyncComponent, reactive, ref } from 'vue';
import SendbirdChat, { BaseChannel, ChannelType, ConnectionHandler, MetaCounter, MetaData, User, UserEventHandler, UserUpdateParams } from "@sendbird/chat";
import { GroupChannelHandler, GroupChannelModule, SendbirdGroupChat } from '@sendbird/chat/groupChannel';
// import Loader from './assets/loader.svg';
import Loader from "./components/Loader.vue";
import { APP_ID } from './Constants';
import SearchBar from './components/SearchBar.vue';
import { BaseMessage } from '@sendbird/chat/message';
interface Props {
    sendbird: SendbirdGroupChat
}
const UsersList = defineAsyncComponent<DefineComponent<Props>>(
    () => import('./components/UsersList.vue') as any
)

const currentChannel =  reactive({});
let loading: Ref<boolean> = ref(true);
// let props = defineProps<{
//     userDetails: object
//     routes?: object
// }>()

// let props = {
//     userDetails: Object,
// }
let userId: string, NICKNAME: string, PROFILE_IMAGE_FILE: string;
let randomString: any = Math.floor(Math.random() * 100);
// if (!props.userDetails) {
if (!localStorage.getItem("userDetails")) {
    let userDetail: object = {
        userId: randomString,
        nickName: 'Anonymous' + '(' + randomString + ')'
    }
    localStorage.setItem("userDetails", JSON.stringify(userDetail))
}

let user: string = localStorage.getItem("userDetails");
console.log(user, JSON.parse(user));
const userDetails: object = JSON.parse(user);
userId = userDetails?.userId.toString();
NICKNAME = userDetails?.nickName;

console.log(userId, NICKNAME)
PROFILE_IMAGE_FILE = "https://images.unsplash.com/photo-1503023345310-bd7c1de61c7d?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=465&q=80";
// } else {
//     userId = props.userDetails?.id;
//     NICKNAME = props.userDetails?.name;
//     PROFILE_IMAGE_FILE = props.userDetails?.profile_image;
// }
const sendbirdChat: any = SendbirdChat.init({
    appId: APP_ID,
    modules: [new GroupChannelModule()],
});

// For Unique Handler Id
const getUniqueHandlerId = () => {
    return (Math.random() + 1).toString(36).substring(7);
}

const connectionHandler = new ConnectionHandler({
    onConnected: () => {
        console.log("SendBird Connected");
    },
    onDisconnected: () => {
        console.log("SendBird Disconnected");
    },
    onReconnectStarted: () => {
        console.log("SendBird Reconnect Started");

    },
    onReconnectSucceeded: async () => {
        console.log("SendBird Reconnect Success");
        // await currentChannel.refresh()
    },
    onReconnectFailed: () => {
        console.log("SendBird Reconnect Failed");
    },
});

sendbirdChat.addConnectionHandler(getUniqueHandlerId(), connectionHandler);

// User Event Handler
const userEventHandler = new UserEventHandler({
    onTotalUnreadMessageCountUpdated: (totalCount: number, countByCustomTypes: object) => {
        console.log("totalCount: ", totalCount, "countByCustomTypes", countByCustomTypes)
    },
});

// Group Channel Handler
const groupChannelHandler = new GroupChannelHandler({
    onMessageReceived: async (channel: BaseChannel, message: BaseMessage) => {
        console.log("channel ", channel)
        console.log("Message received: ", message)
        // if (channel.url == currentChannel.url) {
        //     messageDetailDiv(channel, message);
        //     await channel.markAsRead();
        //     await channel.refresh();
        // }
        // // Check current channel and message received in same platforms like message and Course groups.
        // if (currentChannel.isSuper && channel.isSuper) {
        //     await onMessageSuccess(channel, channel.url == currentChannel.url)
        // } else if (!currentChannel.isSuper && !channel.isSuper) {
        //     await onMessageSuccess(channel, channel.url == currentChannel.url)
        // }
        // channelChangeClick()
    },
    onMessageUpdated: (channel: BaseChannel, message: BaseMessage) => {
        console.log("channel ", channel)
        console.log("Message update: ", message)
    },
    onMessageDeleted: (channel: BaseChannel, messageId: number) => {
        console.log("channel ", channel)
        console.log("Message delete: ", messageId)
    },
    onChannelChanged: (channel: BaseChannel) => {
        console.log("onChannelChanged: ", channel);
    },
    onChannelDeleted: (channelUrl: string, channelType: ChannelType) => {
        console.log("Channel URL: ", channelUrl, "Channel Type: ", channelType)
    },
    onChannelFrozen: (channel: BaseChannel) => {
        console.log("channel ", channel)
    },
    onChannelUnfrozen: (channel: BaseChannel) => {
        console.log("channel ", channel)
    },
    onMetaDataCreated: (channel: BaseChannel, metaData: MetaData) => {
        console.log("channel ", channel)
        console.log("Message received: ", metaData)
    },
    onMetaCounterUpdated: (channel: BaseChannel, metaData: MetaCounter) => {
        console.log("channel ", channel)
        console.log("Message received: ", metaData)
    },
    onMetaCounterDeleted: (channel: BaseChannel, metaDataKeys: string[]) => {
        console.log("channel ", channel)
        console.log("Message received: ", metaDataKeys)
    },
    onChannelHidden: (channel: BaseChannel) => {
        console.log("Chanel Archived: ", channel)
    },
    onUserReceivedInvitation: (channel: BaseChannel, inviter: User, invitees: User[]) => {
        console.log("Channel Details: ", channel, "Inviter: ", inviter, "Invitees: ", invitees)
    },
    onUserDeclinedInvitation: (channel: BaseChannel, inviter: User, invitee: User) => {
        console.log("Channel Details: ", channel, "Inviter: ", inviter, "Invitees: ", invitee)
    },
    onUserJoined: (channel: BaseChannel, user: User) => {
        console.log("Joined User", "Channel Details: ", channel, "User: ", user)
    },
    onUserLeft: (channel: BaseChannel, user: User) => {
        console.log("Left User", "Channel Details: ", channel, "User: ", user)
    },
    onUndeliveredMemberStatusUpdated: (channel: BaseChannel) => {
        console.log("onUndeliveredMemberStatusUpdated", channel)
    },
    onUnreadMemberStatusUpdated: (channel: BaseChannel) => {
        console.log("onUnreadMemberStatusUpdated", channel)
    },
    onTypingStatusUpdated: (channel: BaseChannel) => {
        console.log("Channel Details: ", channel, "Typing Status Updated")
    },
    onUserMuted: (channel: BaseChannel, user: User) => {
        console.log("User Muted", "Channel Details: ", channel, "User: ", user)
    },
    onUserUnmuted: (channel: BaseChannel, user: User) => {
        console.log("User Unmuted", "Channel Details: ", channel, "User: ", user)
    },
    onUserBanned: (channel: BaseChannel, user: User) => {
        console.log("User Banned", "Channel Details: ", channel, "User: ", user)
    },
    onUserUnbanned: (channel: BaseChannel, user: User) => {
        console.log("User Unbanned", "Channel Details: ", channel, "User: ", user)
    },
    onChannelMemberCountChanged: (channels: BaseChannel[]) => {
        console.log("onChannelMemberCountChanged: ", channels)
    },
});

// Add User Event Handler.
sendbirdChat.addUserEventHandler(getUniqueHandlerId(), userEventHandler);

// Add Goup channel Events.
sendbirdChat.groupChannel.addGroupChannelHandler(getUniqueHandlerId(), groupChannelHandler);
sendbirdChat.connect(userId)
    .then((response: any) => {
        console.log(response)
        const userUpdateParams: UserUpdateParams = {
            nickname: NICKNAME,
            profileUrl: PROFILE_IMAGE_FILE,       // Or use profileUrl: PROFILE_URL.
        };
        sendbirdChat.updateCurrentUserInfo(userUpdateParams)
            .then((response: any) => {
                console.log(response)
            })
            .catch((error: any) => console.log(error));
        // console.log(user);
        setTimeout(() => {
            loading.value = false;
        }, 3000)
    })
    .catch((error: any) => console.log(error));
</script>

<template>
    <div>
        <div class="loader" v-if="loading">
            <Loader />
            <!-- <img :src="Loader" /> -->
        </div>
        <div class="" v-else>
            <section class="layout-section">
                <div class="left-sidebar">
                    <div class="contacts-section">
                        <SearchBar />

                        <UsersList :sendbird="sendbirdChat" />
                    </div>
                </div>
                <div class="user-chat-section">
                    <div class="messages-section" v-if="currentChannel">
                        <div class="welcome-screen">

                        </div>
                    </div>
                </div>
            </section>
            <div class="message-section">
                <div class="users-list-container">

                </div>
                <div class="users-chats">
                    Chat
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.loader {
    position: fixed;
    width: 100%;
    height: 100%;
    display: flex;
    place-items: center;
    justify-content: center;
}

.logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
}

.logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
    filter: drop-shadow(0 0 2em #42b883aa);
}

.users-list-container {
    width: 33%;
    border-right: 1px solid rgba(47, 47, 47, 0.1);
    border-bottom: 1px solid rgba(47, 47, 47, 0.1);
}

.layout-section {
    height: 100%;
    overflow-y: hidden;
}

.left-sidebar {
    max-width: 320px;
    width: 100%;
    transition: all .5s ease;
    background: #fff;
    border-right: 1px solid #e8eaf2;
}
.contacts-lists .u_contacts-section {
    flex: 1 1;
}
.contact-section {}

.contact-section {
    height: 100%;
    display: flex;
    flex: 1 1 auto;
    flex-direction: column;
    min-height: 1px;
}
/* Start Chat Screen */
.messages-section {
    height: 100%;
    position: relative;
}

.messages-section .welcome-screen {
    background: url('./assets/ubs-chat-bg.jpg') no-repeat 50%;
    height: 100%;
    display: flex;
    flex: 1 1 auto;
    flex-direction: column;
    min-height: 1px;
    padding: 0 15px;
    background-size: cover;
}
/* End Chat Screen */
</style>
