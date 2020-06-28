<template>
  <div class="map" @mouseleave="currentArea = -1">
      <div class="row" v-for="(row, i) in map" :key="`row-${i}`">
        <Box 
          v-for="(box, j) in row" 
          :key="`row-${i}-box-${j}`"
          :box='box'
          :hoverPaint="currentArea === box"
          :unlocked="unlockedAreas.includes(box)"
          :avaibleToUnlock="areasToUnlock.includes(box)"
          :borders="borderList[i][j]"
          :class="{
            'transition-mode': transitionIsInProgress,
            'in-transition': lastUnlocked === box && transitionIsInProgress
          }"
          @boxHovered="onBoxHovered($event)"
          @boxClicked="onBoxClicked($event)"
        >
         
        </Box>
      </div>
      <Area v-for="(area, id) in areas" 
          :key="`area-${id}`"
          :areaId="id"
          :coords="area"
      />
  </div>  
</template>

<script>
import Box from './Box'
import Area from './Area'
export default {
  components: {
    Box,
    Area
  },
  data() {
    return {
      currentArea: 13,
      lastUnlocked: -1,
      transitionIsInProgress: false,
      map: [
          [0 , 0 , 2 , 2 , 4 , 12, 12, 18, 18, 18, 18, 18, 18,  8, 8 , 8 ,  8, 8 , 8 ],
          [0 , 0 , 2 , 2 , 4 , 4 , 12, 12, 18, 18, 18, 18, 18,  8,  8, 8 ,  8,  8, 8 ],
          [1 , 1 , 1 , 3 , 4 , 4 , 12, 12, 18, 18, 18, 18, 18,  8,  8,  8,  8,  8,  8],
          [1 , 1 , 1 , 3 , 3 , 4 , 12, 12, 12, 11, 11, 18, 18, 11, 11,  8,  8,  8,  8],
          [6 , 1 , 1 , 3 , 3 , 14, 14, 14, 14, 11, 11, 11, 11, 11, 11, 11, 11, 11, 11],
          [6 , 6 , 6 , 9 ,  9, 14, 14, 14, 14, 14, 14, 11, 11, 11, 11, 11, 11, 11, 11],
          [6 , 6 , 9 , 9 ,  9,  5,  5, 14, 14, 14, 14, 14, 14, 14, 14, 11, 11, 11, 11],
          [6 , 6 , 9 , 9 ,  9,  5,  5,  5, 14, 14, 14, 14, 14, 14, 10, 10, 10, 10, 10],
          [6 , 6 , 7 ,  7,  7,  5,  5,  5, 14, 14, 14, 14, 10, 10, 10, 10, 10, 13, 13],
          [6 , 7 , 7 ,  7 , 7,  7,  7, 16, 16, 16, 16, 10, 10, 10, 10, 10, 13, 13, 13],
          [7 , 7 , 7 ,  7 , 7,  7,  7, 16, 16, 16, 16, 10, 10, 10, 10, 13, 13, 13, 13],
          [7 , 7 , 7 ,  7,  7,  7,  7, 16, 16, 16, 16, 10, 10, 10, 13, 13, 13, 13, 13],
          [17, 17, 17, 17, 15, 15, 15, 16, 16, 16, 16, 10, 10, 13, 13, 13, 13, 13, 13],
          [17, 17, 17, 17, 15, 15, 15, 15, 15, 19, 19, 13, 13, 13, 13, 13, 13, 13, 13],
          [17, 17, 17, 17, 15, 15, 15, 15, 15, 19, 13, 13, 13, 13, 13, 13, 13, 13, 13],
          [17, 17, 17, 17, 15, 15, 15, 19, 19, 19, 13, 13, 13, 13, 13, 13, 13, 13, 13],
          [17, 17, 17, 19, 19, 19, 19, 19, 19, 19, 13, 13, 13, 13, 13, 13, 13, 13, 13],
          [17, 17, 19 ,19, 19, 19, 19, 19, 19, 19, 13, 13, 13, 13, 13, 13, 13, 13, 13],
          [19, 19, 19, 19, 19, 19, 19, 19, 19, 19, 13, 13, 13, 13, 13, 13, 13, 13, 13],
        ],
        areas: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19],
        unlockedAreas: [],
      }
    },
    computed: {
      areasToUnlock() {
        if(this.unlockedAreas.length === 0) return [13];
        else {
          let calculatedAreas = [];
          this.unlockedAreas.forEach((area) => {
            this.map.forEach((row, i) => {
              row.forEach((box, j) => {
                if(box === area) {
                  if(i > 0) {
                    this.checkBox(this.map[i-1][j], area, calculatedAreas);
                  }
                  if(j > 0) {
                    this.checkBox(this.map[i][j-1], area, calculatedAreas);
                  }
                  if(this.map[i+1] !== undefined) {
                    this.checkBox(this.map[i+1][j], area, calculatedAreas);
                  }
                  if(this.map[i][j+1] !== undefined) {
                    this.checkBox(this.map[i][j+1], area, calculatedAreas);
                  }
                }
              });
            });
          });
          return calculatedAreas;
        }
      },
      borderList() {
        let calculatedBorders = [];
        let area;
          this.map.forEach((row, i) => {
            calculatedBorders[i] = [];
            row.forEach((box, j) => {
              calculatedBorders[i][j] = {top: 0, right: 0, bottom: 0, left: 0};
              area = box;
                if(i > 0) {
                  if(this.map[i-1][j] !== area) {
                    calculatedBorders[i][j].top = 1;
                  }
                }
                if(j > 0) {
                  if (this.map[i][j-1] !== area) {
                    calculatedBorders[i][j].left = 1;
                  }
                }
                if(this.map[i+1] !== undefined) {
                  if (this.map[i+1][j] !== area) {
                    calculatedBorders[i][j].bottom = 1;
                  }
                }
                if(this.map[i][j+1] !== undefined) {
                  if (this.map[i][j+1] !== area) {
                    calculatedBorders[i][j].right = 1;
                  }
                }
            });
          });
        return calculatedBorders;
      },
      areasCoords() {
        let areas = [];
        this.map.forEach((row, i) => {
          row.forEach((box, j) => {
            if(areas[box] === undefined) areas[box] = [];
            areas[box].push([i, j]);
          });
        });
        return areas;
      }
    },
    watch: {
     
    },
    methods: {
      onBoxHovered(box) {
        this.currentArea = box;
      },
      onBoxClicked(box) {
        if(this.areasToUnlock.includes(box)) {
          this.unlockedAreas.push(box);
          this.areasToUnlock.remove(box);
          this.lastUnlocked = box;
          this.transitionIsInProgress = true;
          setTimeout(() => {
            this.transitionIsInProgress = false;
          }, 500);
        }
        // else if(this.unlockedAreas.includes(box)) {
        //   this.unlockedAreas.remove(box);
        //   this.areasToUnlock.push(box);
        // }
      },
      checkBox(id, area, calculatedAreas) {
        if(id !== area) {
          if (!this.unlockedAreas.includes(id) && !calculatedAreas.includes(id)) {
            calculatedAreas.push(id);
          }
        }
      }
    }
}
</script>

<style lang="scss">
.map {
  width: 640px;
  margin: 50px auto;
  background: #f2f2f2;
  background: url('../assets/maps/map1.gif');
  background-size: cover;
  border: 3px solid rgb(35, 39, 46);
  border-radius: 3px;
  overflow: hidden;
}
.row {
  display: flex;
}
.transition-mode {
  transition: all .4s ease-in-out;
  transition-delay: .4s;
  .box-inner {
    transition: all .4s ease-in-out;
    transition-delay: .4s;
  }
}
.in-transition {
  transition: all .4s ease-in-out;
  transition-delay: 0s;
  .box-inner {
    transition: all .4s ease-in-out;
    transition-delay: 0s;
  }
}
</style>