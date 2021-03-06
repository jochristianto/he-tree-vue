<template>
  <div class="p-4">
    <div class="text-center">
      <div class="h5 font-weight-normal text-uppercase mb-0">Intergration Example of</div>
      <div class="h3">he-tree-vue</div>
    </div>

    <div class="text-center">
      <div class="mt-4">
        The example covers how to get the updated nodes when a drop event is triggered.<br />
        The full documentation of the component can be found here,
        <a href="https://he-tree-vue.phphe.com" target="_blank">https://he-tree-vue.phphe.com</a>.
      </div>

      <div class="mt-4">
        <ul>
          <li><a href="https://he-tree-vue-usage.netlify.app" target="_blank" class="btn btn-outline-primary">Online Preview</a></li>
          <li><a href="https://github.com/jochristianto/he-tree-vue" target="_blank" class="btn btn-outline-secondary">Source Code</a></li>
          <li><a href="https://www.npmjs.com/package/he-tree-vue" target="_blank" class="btn btn-outline-success">he-tree-vue on NPM</a></li>
        </ul>
      </div>
    </div>

    <hr class="my-4" />

    <div class="mt-4">
      <div class="row">
        <div class="col-4">
          <h5>Tree View</h5>
          <div class="mt-3">
            <Tree :value="treeData" @drop="onDrop">
              <template slot-scope="{ node, index, path, tree }">
                <div class="align-content-start align-items-start d-flex justify-content-start">
                  <div>
                    <button
                      @click="tree.toggleFold(node, path)"
                      class="btn"
                      :class="{ 'btn-primary': node.$folded, 'btn-success': !node.$folded }"
                      style="width: 38px;"
                    >
                      {{ node.$folded ? '+' : '-' }}
                    </button>
                  </div>
                  <div class="ml-3">
                    <div class="font-weight-bold">{{ node.name }}</div>
                    <div class="small text-secondary">ID: {{ node.id }} - {{ node.name }} - Index: {{ index }}</div>
                  </div>
                </div>
              </template>
            </Tree>
          </div>
        </div>
        <div class="col-4">
          <h5>Tree Object</h5>
          <div class="mt-3">
            <VueJsonPretty :data="treeData" :deep="3" :show-length="true" :show-line="true" />
          </div>
        </div>
        <div class="col-4">
          <h5>Update Object</h5>
          <div class="mt-3">
            <VueJsonPretty :data="updates" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-lonely-if */
import VueJsonPretty from 'vue-json-pretty';
import { Tree, Draggable, Fold } from 'he-tree-vue';
import 'he-tree-vue/dist/he-tree-vue.css'; // base style
import 'bootstrap/dist/css/bootstrap.min.css';

export default {
  components: {
    Tree: Tree.mixPlugins([Draggable, Fold]),
    VueJsonPretty,
  },
  data() {
    return {
      treeData: [
        {
          id: 911,
          parent_id: null,
          name: 'API',
          description: 'API',
          children: [],
        },
        {
          id: 1,
          parent_id: null,
          name: 'Client Page',
          description: 'Client Page',
          children: [
            {
              id: 2,
              parent_id: 1,
              index: 0,
              name: 'Access Homepage/Landing Page',
              children: [],
            },
            {
              id: 3,
              parent_id: 1,
              name: 'Access Login page',
              children: [],
            },
            {
              id: 4,
              parent_id: 1,
              name: 'Access Logout page',
              children: [],
            },
            {
              id: 5,
              parent_id: 1,
              name: 'Access Dashboard',
              children: [],
            },
            {
              id: 6,
              parent_id: 1,
              name: 'Access Attendance',
              children: [],
            },
            {
              id: 26,
              parent_id: 1,
              name: 'Access Leave-related pages',
              children: [
                {
                  id: 27,
                  parent_id: 26,
                  name: 'Access Leave History',
                  children: [],
                },
                {
                  id: 28,
                  parent_id: 26,
                  name: 'Access Leave Balance',
                  children: [],
                },
                {
                  id: 29,
                  parent_id: 26,
                  name: 'Access Leave Request',
                  children: [
                    {
                      id: 30,
                      parent_id: 29,
                      name: 'Access update feature for Leave Request',
                      children: [],
                    },
                    {
                      id: 31,
                      parent_id: 29,
                      name: 'Access view feature for Leave Request',
                      children: [],
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],

      updates: [],
    };
  },
  methods: {
    onDrop(e) {
      let startParentPath = e.startTree.getNodeParentByPath(e.startPath);
      let targetParentPath = e.targetTree.getNodeParentByPath(e.targetPath);

      // If root element get dropped
      if (typeof startParentPath === 'undefined') startParentPath = e.startTree.rootNode;
      if (typeof targetParentPath === 'undefined') targetParentPath = e.targetTree.rootNode;

      const sameParent = startParentPath === targetParentPath;

      if (typeof startParentPath !== 'undefined' && typeof targetParentPath !== 'undefined') {
        // If not root element get dropped
        this.updates = this.process(startParentPath);
        if (!sameParent) {
          this.updates = [...this.updates, ...this.process(targetParentPath)];
        }
      }
    },
    process(path) {
      let updates = [];
      if (path && path.children) updates = path.children.map((child, index) => ({ id: child.id, parent_id: path.id || null, index }));
      return updates;
    },
  },
};
</script>

<style lang="scss" scoped>
ul {
  list-style: none;

  li {
    display: inline-block;
    margin-left: 0.5rem;

    &:first-child {
      margin-left: 0;
    }
  }
}
</style>
