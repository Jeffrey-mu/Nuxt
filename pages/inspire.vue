<template>
    <section class="section">
        <canvas ref="el" width="1000" height="1000" scale-50 origin-top-left />
    </section>
</template>

<script>
export default {
    data () {
        return {
            el: null,
            pendingTasks: [],
            framesCount: 0
        }
    },
    computed: {
        ctx() {
            return this.el.getContext('2d')
        }
    },
    mounted () {
        this.el = this.$refs.el
        this.init()
        this.startFrame()
    },
    methods: {
        init () {
            this.ctx.strokeStyle = '#444'
            this.step({
                start: { x: 0, y: 0 },
                length: 8,
                theta: Math.PI / 4,
            })
        },
        step (b, depth = 0) {
            const end = this.getEndPoint(b)
            this.drawBranch(b)

            if (depth < 4 || Math.random() < 0.5) {
                this.pendingTasks.push(() => this.step({
                    start: end,
                    length: b.length + (Math.random() * 2 - 1),
                    theta: b.theta - 0.2 * Math.random(),
                }, depth + 1))
            }
            if (depth < 4 || Math.random() < 0.5) {
                this.pendingTasks.push(() => this. step({
                    start: end,
                    length: b.length + (Math.random() * 2 - 1),
                    theta: b.theta + 0.2 * Math.random(),
                }, depth + 1))
            }
        },
        frame () {
            const tasks = []
            this.pendingTasks = this.pendingTasks.filter((i) => {
                if (Math.random() > 0.4) {
                    tasks.push(i)
                    return false
                }
                return true
            })
            tasks.forEach(fn => fn())
        },
        startFrame () {
            requestAnimationFrame(() => {
                this.framesCount += 1
                if (this.framesCount % 3 === 0)
                    this.frame()
                this.startFrame()
            })
        }, 
        drawBranch (b) {
            this.lineTo(b.start, this.getEndPoint(b))
        }, 
        lineTo (p1, p2) {
            this.ctx.beginPath()
            this.ctx.moveTo(p1.x, p1.y)
            this.ctx.lineTo(p2.x, p2.y)
            this.ctx.stroke()
        }, 
        getEndPoint (b) {
            return {
                x: b.start.x + b.length * Math.cos(b.theta),
                y: b.start.y + b.length * Math.sin(b.theta),
            }
        }
    }
}

</script>
