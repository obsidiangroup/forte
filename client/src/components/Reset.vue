<template>
    <div ref="modalEl" class="modal" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Quick Options</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <ul class="list-group">
                        <li class="list-group-item list-group-item-action clickable" @click="reset">Reset</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { Modal } from "bootstrap"

let modal = null;
const modalEl = ref(null);

async function reset() {
    console.log("Resetting...");
    localStorage.clear();
    sessionStorage.clear();
    window.location.replace("/");
}

async function show() {
    modal.show();
}

async function hide() {
    modal.hide();
}

defineExpose({
    show,
    hide
})

onMounted(() => {
    modal = new Modal(modalEl.value);
});
</script>