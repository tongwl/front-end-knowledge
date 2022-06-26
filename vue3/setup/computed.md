# computed

**computed返回的值也是ref类型**

```vue
<template>
  <div>{{count}}</div>
  <div>{{double}}</div>
  <div><button @click="increase">Increase</button></div>
</template>

<script>
import { ref, computed } from 'vue'
export default {
  name: "Test4",
  setup() {
    const count = ref(0)

    const double = computed(() => count.value * 2)
    console.log(double) // ComputedRefImp {..., value: 0}

    const increase = () => {
      count.value++
    }
    return {
      count,
      double,
      increase
    }
  }
}
</script>
```

**但是写在reactive中的computed，返回的并不是ref类型**

```vue
<template>
  <div>{{count}}</div>
  <div>{{double}}</div>
  <button @click="increase">Increase</button>
</template>

<script lang="ts">
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
}
import { reactive, computed, ref, toRef, toRefs } from 'vue'
export default {
  name: "Test5",
  setup() {
    const data: DataProps = reactive({
      count: 0,
      double: computed(() => data.count * 2), //此时的double并不是Ref类型,而是number类型
      increase: () => {
        data.count++
      }
    })

    console.log(data.double) // 0, 它不是Ref类型，它是number类型

    return {
      count: toRef(data, 'count'),
      double: toRef(data, 'double'), //因为double不是ref类型，所以还是需要toRef去转换
      increase: toRef(data, 'increase')
    }
  }
}
</script>

<style scoped>
</style>
```

