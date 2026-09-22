
<script setup>

const author = "R. Casadei, P. Baldini, N. Castronuovo"
const logoUrl = '/imgs/unibo.png'
</script>

<template>
    <div class="global-bottom" aria-hidden="true">
        <div class="global-bottom__inner">
            <div class="author"><span class="authors">{{ author }}</span> - University of Bologna</div>
            <div class="counter">{{ $slidev.nav.currentPage }} / {{ $slidev.nav.total }}</div>
        </div>
        <div class="logo-wrap">
            <!-- <img src="/imgs/unibo.png" alt="logo" class="logo" /> -->
        </div>
    </div>
</template>

<style scoped>
.global-bottom {
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgb(187, 46, 41); /* red unibo */
    color: white;
    pointer-events: none;
    z-index: 9999;
    /*font-weight: bold;*/
}
.global-bottom__inner {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px 20px;
    font-size: 0.75em;
    opacity: 0.8;
    /*font-weight: 500;*/
}
.logo-wrap {
    margin-left: 12px;
}
.logo {
    position: fixed;
    right: -3em;
    bottom: -2em;
    width: 15em;
    opacity: 0.2;
    /* transform: rotate(-45deg); */
    /* z-index: -1; */
}
.authors {
    font-weight: 900;
    font-family: sans serif;
}
</style>
