#include <stdio.h>
#include <stdlib.h> 


struct node{
	int data; // data persistence
	struct node * left; // left sub tree root node link
	struct node * right; // right sub tree root node link
	struct node * mother; // mother node link
	struct node * leftBig; // left sub tree biggest value node link	
};
typedef struct node Node;

Node * root=NULL;

// Finding position 
Node * findPosition(int data, Node *current) {
	if(data > current->data){
		if(current->right==NULL) return current;
		else return findPosition(data, current->right);
	}
	else {
		if(current->left==NULL) return current;
		else return findPosition(data, current->left);
	}
}

// Finding biggest data
Node * findBiggest(Node * current) {
	if(current->right !=NULL ) return findBiggest(current->right);
	else return current; 
 }

// Updating left big 
void updateLeftBig(Node * current) {
	if(current==NULL)  return;
	
	updateLeftBig(current->left);
	updateLeftBig(current->right);
	
	if(current->left != NULL) current->leftBig = findBiggest(current->left);
	else 
	    current->leftBig= NULL;
}

// Adding new node
void add(int data) {
	Node *newNode=(Node*)malloc(sizeof(Node));
	newNode->data=data;
	newNode->left=NULL;
	newNode->right=NULL;
	newNode->mother=NULL;
	newNode->leftBig=NULL;
	
	if(root==NULL){
	root=newNode;	
	} else{
		Node *position= findPosition(newNode->data, root);

	    if(position->data > newNode->data) {
		   position->left=newNode;
		   newNode->mother=position;
	}else {
		position->right=newNode;
		newNode->mother=position;
	}
  } 
	updateLeftBig(root);
}
 
// Finding data
Node * findData(int data, Node *current) {
	if(current==NULL) return NULL;
	
	if(current->data==data) return current;
	
	if(current->data < data) {
		if(current->right==NULL) return NULL;
		else return findData(data, current->right);
		
	} else {
		if(current->left==NULL) return NULL;
		else return findData(data, current->left);
	}
	
}

// Finding parent
Node * findParent(int data, Node * current) {
	if(data== root->data) return NULL;
	
	if(current->right !=NULL) {
		if(current->right->data==data) return current;
	}
	
	if(current->left !=NULL) {
		if(current->left->data ==data) return current;
	}
	
	if(current->data < data) return findParent(data, current->right);
	
	else return findParent(data, current->left);
	
}

// In-order
void inorder(Node * current) {
	if(current==NULL) return;
	
	if(current->left!=NULL) inorder(current->left);
	
	printf("Data: %d ", current->data);
	
	if(current->mother!=NULL) printf("Mother: %d  ",current->mother->data);
	else printf("Mother:NULL.");
	  
	  if(current->leftBig !=NULL) printf("LeftBig: %d", current->leftBig->data);
	  else printf("LeftBig:NULL.");
	  
	  printf("\n");
	  if(current->right!=NULL) inorder(current->right);
}

// Deleting 
void del(int data) {
	Node * cur=root;
	Node * par= root;
	
	cur= findData(data, cur);
	
	if(cur !=NULL) {
		// Leaf node 
		if(cur->left==NULL && cur->right==NULL) {
			par=findParent(cur->data, par);
			if(par!=NULL) {
				if(par->data < cur->data) par->right=NULL;
				else par->left=NULL;
			} 
			free(cur);
		}
		
		// Only left child
		else if(cur->left!=NULL && cur->right == NULL) {
			par= findParent(cur->data, root);  
			if(par!=NULL) {
				if(par->left==cur) par->left=cur->left;
				else par->right=cur->left; 
			}
			cur->left->mother=par;
			free(cur);
		}
		
		// Only right child
		else if(cur->left==NULL && cur->right!=NULL) {
			par= findParent(cur->data, root);
			if(par!=NULL) {
				if(par->left==cur) par->left=cur->right;
				else par->right= cur->right;
			}
			cur->right->mother=par;
			free(cur);
		}
		
		// Two children
		else {
			Node *biggest= findBiggest(cur->left);
			int temp=biggest->data;
			del(biggest->data);
			cur->data=temp;
		}
		
	}
	updateLeftBig(root);
}

int main() {
	root=NULL;
	add(50);
	add(30);
	add(70);
	add(20);
	add(40);
	add(60);
	add(80);
	
	printf("Initial Tree:\n");
	inorder(root);
	printf("\n");
	
	// Searching
	Node *found= findData(40, root);
	if(found!=NULL) printf("Current value is %d\n", found->data);
	else printf("Data was not found!...\n");
	
	// Deleting
	printf("30 is deleting...\n");
	del(30);
	inorder(root);
	return 0;
	
	
}
