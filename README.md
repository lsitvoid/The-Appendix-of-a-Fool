# The-Appendix-of-a-Fool

BLACK = True
RED = False

class Node:
    __slots__ = ('key', 'color', 'left', 'right', 'parent')
    
    def __init__(self, key, color=RED):
        self.key = key
        self.color = color
        self.left = None
        self.right = None
        self.parent = None

class RedBlackTree:
    def __init__(self):
        self.nil = Node(None, BLACK)  # 哨兵节点（叶子节点）
        self.root = self.nil
    
    def insert(self, key):
        new_node = Node(key)
        new_node.left = self.nil
        new_node.right = self.nil
        parent = None
        current = self.root
        
        # 查找插入位置
        while current != self.nil:
            parent = current
            if new_node.key < current.key:
                current = current.left
            else:
                current = current.right
                
        new_node.parent = parent
        
        if parent is None:
            self.root = new_node
        elif new_node.key < parent.key:
            parent.left = new_node
        else:
            parent.right = new_node
            
        new_node.color = RED  # 新插入节点初始为红色
        self._fix_insert(new_node)
    
    def _fix_insert(self, node):
        while node.parent and node.parent.color == RED:
            if node.parent == node.parent.parent.left:
                uncle = node.parent.parent.right
                if uncle.color == RED:
                    # Case 1：叔节点是红色
                    node.parent.color = BLACK
                    uncle.color = BLACK
                    node.parent.parent.color = RED
                    node = node.parent.parent
                else:
                    if node == node.parent.right:
                        # Case 2：叔节点是黑色且当前节点是右子节点
                        node = node.parent
                        self._left_rotate(node)
                    # Case 3：叔节点是黑色且当前节点是左子节点
                    node.parent.color = BLACK
                    node.parent.parent.color = RED
                    self._right_rotate(node.parent.parent)
            else:
                uncle = node.parent.parent.left
                if uncle.color == RED:
                    node.parent.color = BLACK
                    uncle.color = BLACK
                    node.parent.parent.color = RED
                    node = node.parent.parent
                else:
                    if node == node.parent.left:
                        node = node.parent
                        self._right_rotate(node)
                    node.parent.color = BLACK
                    node.parent.parent.color = RED
                    self._left_rotate(node.parent.parent)
        
        self.root.color = BLACK  # 根节点始终为黑色
    
    def delete(self, key):
        node = self._find_node(key)
        if node == self.nil:
            return
            
        original_color = node.color
        if node.left == self.nil:
            # 无左子节点/无子节点
            x = node.right
            self._transplant(node, node.right)
        elif node.right == self.nil:
            # 无右子节点
            x = node.left
            self._transplant(node, node.left)
        else:
            # 有两个子节点
            successor = self._minimum(node.right)
            original_color = successor.color
            x = successor.right
            if successor.parent == node:
                x.parent = successor
            else:
                self._transplant(successor, successor.right)
                successor.right = node.right
                successor.right.parent = successor
            self._transplant(node, successor)
            successor.left = node.left
            successor.left.parent = successor
            successor.color = node.color
            
        if original_color == BLACK:
            self._fix_delete(x)
    
    def _fix_delete(self, node):
        while node != self.root and node.color == BLACK:
            if node == node.parent.left:
                sibling = node.parent.right
                if sibling.color == RED:
                    # Case 1：兄弟节点是红色
                    sibling.color = BLACK
                    node.parent.color = RED
                    self._left_rotate(node.parent)
                    sibling = node.parent.right
                
                if sibling.left.color == BLACK and sibling.right.color == BLACK:
                    # Case 2：兄弟节点的两个子节点都是黑色
                    sibling.color = RED
                    node = node.parent
                else:
                    if sibling.right.color == BLACK:
                        # Case 3：兄弟节点的右子节点是黑色
                        sibling.left.color = BLACK
                        sibling.color = RED
                        self._right_rotate(sibling)
                        sibling = node.parent.right
                    # Case 4：兄弟节点的右子节点是红色
                    sibling.color = node.parent.color
                    node.parent.color = BLACK
                    sibling.right.color = BLACK
                    self._left_rotate(node.parent)
                    node = self.root
            else:
                sibling = node.parent.left
                if sibling.color == RED:
                    sibling.color = BLACK
                    node.parent.color = RED
                    self._right_rotate(node.parent)
                    sibling = node.parent.left
                
                if sibling.right.color == BLACK and sibling.left.color == BLACK:
                    sibling.color = RED
                    node = node.parent
                else:
                    if sibling.left.color == BLACK:
                        sibling.right.color = BLACK
                        sibling.color = RED
                        self._left_rotate(sibling)
                        sibling = node.parent.left
                    sibling.color = node.parent.color
                    node.parent.color = BLACK
                    sibling.left.color = BLACK
                    self._right_rotate(node.parent)
                    node = self.root
        node.color = BLACK
    
    def _transplant(self, u, v):
        """用子树v替换子树u"""
        if u.parent is None:
            self.root = v
        elif u == u.parent.left:
            u.parent.left = v
        else:
            u.parent.right = v
        v.parent = u.parent
    
    def _minimum(self, node):
        """找到以node为根的子树的最小节点"""
        while node.left != self.nil:
            node = node.left
        return node
    
    def _find_node(self, key):
        """根据key查找节点"""
        current = self.root
        while current != self.nil and current.key != key:
            if key < current.key:
                current = current.left
            else:
                current = current.right
        return current
    
    def _left_rotate(self, x):
        """左旋操作"""
        y = x.right
        x.right = y.left
        if y.left != self.nil:
            y.left.parent = x
            
        y.parent = x.parent
        if x.parent is None:
            self.root = y
        elif
