<template>
  <div>
    Тестовое
  </div>
</template>

<script setup lang="ts">
interface Item {
  id: number | string | undefined;
  parent: number | string | undefined;
  type?: string | null;
}

class TreeStore {
  private readonly array: Item[] = []
  private treeIndexedByIds: Map<Item['id'], Item>
  private childrensByParentId: Map<Item['parent'], Item[]>

  constructor(array: Item[]) {
    this.array = array

    this.treeIndexedByIds = array.reduce((acc, i) => {
      acc.set(i.id, i)

      return acc
    }, new Map())

    this.childrensByParentId = array.reduce((acc, i) => {
      acc.has(i.parent) ? acc.get(i.parent)?.push(i) : acc.set(i.parent, [i])

      return acc;
    }, new Map())
  }

  public getAll(): Item[] {
    return this.array;
  }

  public getItem(id: Item['id']): Item | undefined {
    return this.treeIndexedByIds.get(id)
  }

  public getChildren(id: Item['id']): Item[] {
    return this.childrensByParentId.get(id) ?? []
  }

  public getAllChildren(id: Item['id']): Item[] {
    let result = this.getChildren(id);

    for (const children of result) {
      if (this.getChildren(children.id)) {
        result = result.concat(this.getAllChildren(children.id))
      }
    }

    return result;
  }

  public getAllParents(id: Item['id']): Item[] {
    const parentId = this.getItem(id)?.parent
    const parentItem = this.getItem(parentId)

    if (!parentItem) return []

    return [parentItem, ...this.getAllParents(parentId)]
  }
}

const items = [
  { id: 1, parent: 'root' },
  { id: 2, parent: 1, type: 'test' },
  { id: 3, parent: 1, type: 'test' },

  { id: 4, parent: 2, type: 'test' },
  { id: 5, parent: 2, type: 'test' },
  { id: 6, parent: 2, type: 'test' },

  { id: 7, parent: 4, type: null },
  { id: 8, parent: 4, type: null },
];

const ts = new TreeStore(items);

console.log(ts.getAll())
console.log(ts.getItem(7))
console.log(ts.getChildren(4))
console.log(ts.getChildren(5))
console.log(ts.getChildren(2))
console.log(ts.getAllChildren(2))
console.log(ts.getAllParents(7))
</script>

<style scoped>

</style>
