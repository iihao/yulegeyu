<template>
  <div id="gamePage">
    <a-row align="space-between">
      <a-button style="margin-bottom: 8px" @click="doBack"> 返回</a-button>
      <a-button>块数：{{ clearBlockNum }} / {{ totalBlockNum }}</a-button>
    </a-row>
    <!-- 胜利 -->
    <a-row align="center">
      <div v-if="gameStatus === 3" style="text-align: center">
        <h2>恭喜，你赢啦！🎉</h2>
        <img alt="程序员鱼皮" src="../assets/kunkun.png" />
        <my-git style="margin-top: 16px" />
      </div>
    </a-row>
    <!-- 分层选块 -->
    <a-row align="center">
      <div v-show="gameStatus > 0" class="level-board">
        <div v-for="(block, idx) in levelBlocksVal" :key="idx">
          <div
            v-if="block.status === 0"
            class="block level-block"
            :class="{
              disabled: !isHolyLight && block.lowerThanBlocks.length > 0,
            }"
            :data-id="block.id"
            :style="{
              zIndex: 100 + block.level,
              left: block.x * widthUnit + 'px',
              top: block.y * heightUnit + 'px',
            }"
            @click="() => doClickBlock(block)"
          >
            <!-- {{ block.type }} -->
            <img
              :src="getAssetsFile(`${block.type}.png`)"
              width="30"
              height="30"
            />
          </div>
        </div>
      </div>
    </a-row>
    <!-- 随机选块 -->
    <a-row align="space-between" class="random-board">
      <div
        v-for="(randomBlock, index) in randomBlocksVal"
        :key="index"
        class="random-area"
      >
        <div
          v-if="randomBlock.length > 0"
          :data-id="randomBlock[0].id"
          class="block"
          @click="() => doClickBlock(randomBlock[0], index)"
        >
          <!-- {{ randomBlock[0].type }} -->
          <img
            :src="getAssetsFile(`${randomBlock[0].type}.png`)"
            width="30"
            height="30"
          />
        </div>
        <!-- 隐藏 -->
        <div
          v-for="num in Math.max(randomBlock.length - 1, 0)"
          :key="num"
          class="block disabled"
        >
          <span v-if="canSeeRandom">
            <!-- {{ randomBlock[num].type }} -->
            <img
              :src="getAssetsFile(`${randomBlock[num].type}.png`)"
              width="30"
              height="30"
            />
          </span>
        </div>
      </div>
    </a-row>
    <!-- 槽位 -->
    <a-row v-if="slotAreaVal.length > 0" align="center" class="slot-board">
      <div v-for="(slotBlock, index) in slotAreaVal" :key="index" class="block">
        <!-- {{ slotBlock?.type }} -->
        <img
          v-if="slotBlock?.type != undefined"
          :src="getAssetsFile(`${slotBlock?.type}.png`) || ''"
          width="30"
          height="30"
        />
      </div>
    </a-row>
    <!-- 技能 -->
    <div class="skill-board">
      <a-space>
        <a-button size="small" @click="doRevert">撤回</a-button>
        <a-button size="small" @click="doRemove">移出</a-button>
        <a-button size="small" @click="doShuffle">洗牌</a-button>
        <a-button size="small" @click="doBroke">破坏</a-button>
        <a-button size="small" @click="doHolyLight">圣光</a-button>
        <a-button size="small" @click="doSeeRandom">透视</a-button>
      </a-space>
    </div>
  </div>
</template>

<script setup lang="ts">
import useGame from "../core/game";
import { onMounted } from "vue";
import { useRouter } from "vue-router";
import MyGit from "../components/MyAd.vue";

const router = useRouter();

//获取图片资源
const getAssetsFile = (url: string) => {
  return new URL(`../assets/icon/${url}`, import.meta.url).href;
};
const {
  gameStatus,
  levelBlocksVal,
  randomBlocksVal,
  slotAreaVal,
  widthUnit,
  heightUnit,
  totalBlockNum,
  clearBlockNum,
  isHolyLight,
  canSeeRandom,
  doClickBlock,
  doStart,
  doShuffle,
  doBroke,
  doRemove,
  doRevert,
  doHolyLight,
  doSeeRandom,
} = useGame();

/**
 * 回上一页
 */
const doBack = () => {
  router.back();
};

onMounted(() => {
  doStart();
});
</script>

<style scoped>
.level-board {
  position: relative;
}

.level-block {
  position: absolute;
}

.random-board {
  margin-top: 8px;
}

.random-area {
  margin-top: 8px;
}

.slot-board {
  border: 10px solid saddlebrown;
  margin: 16px auto;
  width: fit-content;
}

.skill-board {
  text-align: center;
}

.block {
  font-size: 28px;
  width: 42px;
  height: 48px;
  line-height: 42px;
  /* border: 1px solid #eee; */
  background-image: url("../assets/icon/01.png");
  background-position: center center;
  /* 让背景图基于容器大小伸缩 */
  background-size: cover;
  text-align: center;
  vertical-align: top;
  display: inline-block;
}

.disabled {
  background-image: url("../assets/icon/02.png"), url("../assets/icon/01.png");
  cursor: not-allowed;
}
img {
  margin-bottom: 3px;
}
</style>
