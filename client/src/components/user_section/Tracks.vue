<template>
    <ul class="nav nav-pills">
        <li class="nav-item">
            <router-link :to="'/user/' + username" class="nav-link search-link">Profile</router-link>
        </li>
        <li class="nav-item">
            <router-link :to="'/user/' + username + '/history'" class="nav-link search-link">Listening
                History</router-link>
        </li>
        <li class="nav-item">
            <router-link :to="'/user/' + username + '/tracks'" class="nav-link bg-dark search-link text-white">Favorite
                Tracks</router-link>
        </li>
        <li class="nav-item">
            <router-link :to="'/user/' + username + '/playlists'" class="nav-link search-link">Playlists</router-link>
        </li>
        <li class="nav-item">
            <router-link :to="'/user/' + username + '/albums'" class="nav-link search-link">Albums</router-link>
        </li>
        <li class="nav-item">
            <router-link :to="'/user/' + username + '/artists'" class="nav-link search-link">Artists</router-link>
        </li>
        <li class="nav-item">
            <router-link :to="'/user/' + username + '/friends'" class="nav-link search-link">Friends</router-link>
        </li>
    </ul>
    <hr />
    <ul class="list-group">
        <li class="list-group-item bg-dark text-light d-flex">
            <div class="d-flex w-100 justify-content-between">
                <div>
                    <span class="fw-bold">{{ total }} liked tracks</span>
                </div>
            </div>
        </li>
        <li v-if="tracks.length == 0" class="list-group-item list-group-item-action d-flex justify-content-between">
            <div class="d-flex flex-fill align-items-center">
                <div class="d-flex flex-column">
                    <span class="fw-bold">No tracks found</span>
                </div>
            </div>
        </li>
        <li class="list-group-item list-group-item-action d-flex justify-content-between" v-for="track in tracks"
            @contextmenu.prevent="right_click({ item: track, event: $event })">
            <div class="d-flex flex-fill align-items-center">
                <img :src="track.cover" class="playlist-selection-img me-2" />
                <div class="d-flex flex-column">
                    <button class="btn btn-link search-link" :content_id="track.id" :content_type="track.type"
                        @click="playTrack(track.id)" style="display:contents;">
                        {{ track.title }}
                    </button>
                </div>
            </div>
        </li>
    </ul>
    <div class="d-flex justify-content-end mt-2">
        <button v-show="searchFinished" type="button" class="btn btn-dark" @click="get_tracks">Load more</button>
        <button v-show="!searchFinished" class="btn btn-dark" type="button" disabled>
            <span class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
            Loading...
        </button>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import { right_click, action } from '/js/events.js';

const router = useRouter();
const username = ref(router.currentRoute.value.params.id);

const tracks = ref([]);
const total = ref(0);
const offset = ref(0);
const searchFinished = ref(true);

// Must be synchronized in groupSession: ok
async function playTrack(track_id) {
    action({
        func: async function op() {
            ft.playTrack(track_id)
        },
        object: track_id,
        operation: "playTrack"
    })
    return;
}

async function get_tracks() {
    if (!searchFinished.value) {
        return
    }
    searchFinished.value = false;

    let data = await ft.API(`/user/${username.value}/tracks/${offset.value}`);
    if (!data) return;

    total.value = data.total;
    for (let i = 0; i < data.tracks.length; i++) {
        let track = data.tracks[i];
        if (tracks.value.includes(track)) {
            continue;
        }
        tracks.value.push(track);
        offset.value += 1;
    }
    searchFinished.value = true;
}

onMounted(() => {
    get_tracks();
})
</script>