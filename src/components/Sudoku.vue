<template>
<div class="sudoku">
    <div class="grid">
        <div class="row" v-for="(row, rowIndex) in puzzle" :key="rowIndex">
            <div class="cell" 
            :class="{'border-right': colIndex === 2 || colIndex=== 5,
            'border-bottom' : rowIndex === 2 || rowIndex === 5, 
            'original': cell.original,
            'active': activeRow === rowIndex && activeCol === colIndex,
            'invalid': cell.value && cellInvalid(rowIndex, colIndex, cell.value)
            }"
            v-for="(cell, colIndex) in row" :key="colIndex"
            @click="setCellActive(rowIndex, colIndex, cell.original)"
            >
                {{ cell.value }}
            </div>
        </div>
    </div>

    <div class="row">
       <button type="button" class="btn" 
       v-for="value in Array(9).keys()" 
       :key="value" 
       :disabled="activeRow === -1 || activeCol === -1"
       @click="setCellValue(value + 1)"
       >
           {{ value + 1 }}
       </button>
    </div>
</div>
</template>
<script>
import { sudoku } from 'sudoku.js/sudoku.js';

export default {
    name: 'Sudoku',
    data () {
        return {
            puzzle: [],
            difficulty: 'easy',
            activeRow: -1,
            activeCell: -1
        }
    },
    mounted () {
        this.generatePuzzle()
    },
    methods: {
        generatePuzzle() {
            const boardString = sudoku.generate(this.difficulty);
            this.puzzle = sudoku.board_string_to_grid(boardString)
            .map(row => {
                return row.map(cell => {
                    return {
                        value: cell !== '.' ? parseInt(cell) : null,
                        original: cell !== '.'
                    }
                })
            })
        },
        setCellActive(row, col, original) {
            if(original) {
                return
            }
            else if(this.activeRow === row && this.activeCol === col) {
                this.activeRow = -1
                this.activeCol = -1
                return
            }
            this.activeRow = row
            this.activeCol = col
        },
        setCellValue(value) {
            this.puzzle[this.activeRow][this.activeCol].value = value
            this.activeRow = -1
            this.activeCol = -1

        },
        cellInvalid(row, col, value) {
            if(!value) {
                return true
            }

            for(let c = 0; c < 9; c++) {
                if(this.puzzle[row][c].value === value && c != col) {
                    return true
                }
            }
            for(let r = 0; r < 9; r++) {
                if(this.puzzle[r][col].value === value && r != row) {
                    return true
                }
            }

            return false
        }   
    }
}
</script>

<style scoped>
.sudoku {
    width: 100%;
    max-width: 420px;
    margin: 0.5rem auto;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}

.grid {
  width: calc(9 * 40px);
  margin: 0.5rem auto 1rem;
}

.row {
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.cell {
    display: block;
    width: 40px;
    height: 40px;
    box-sizing: border-box;
    border: 1px solid #bbb;
    font-size: 24px;
    line-height: 40px;
    text-align: center;
    cursor:default;
}

.cell.border-right {
    border-right-width: 3px;
}

.cell.border-bottom {
    border-bottom-width: 3px;
}

.cell.original {
    font-weight: bold;
}

.cell:not(.original) {
    cursor: pointer;
}

.cell.active {
    background-color: peachpuff;
    color:cornflowerblue
}

.cell.invalid:not(.active) {
    background-color: salmon;
    color:seashell;
}

.btn {
    width: 38px;
    height: 38px;
    font-size: 24px;
    cursor: pointer;
}

.btn:disabled {
    cursor: not-allowed;
}
</style>