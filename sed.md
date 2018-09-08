## sed

	
	sed全名叫stream editor，流编辑器，用程序的方式来编辑文本，相当的hacker啊
	
	sed基本上就是玩正则模式匹配，所以，玩sed的人，正则表达式一般都比较强。
	

- s命令

		
		cat pets.txt
		
		This is my cat
		my cat's name is betty
		This is my dog
		my dog's name is frank
		This is my fish
		my fish's name is george
		This is my goat
		my goat's name is adam
		
		把其中的my字符串替换成Hao Chen’s
		
		下面的语句应该很好理解 s表示替换命令，/my/表示匹配my
		
		/Hao Chen’s/表示把匹配替换成Hao Chen’s，/g 表示一行上的替换所有的匹配
		
		命令:
		
		sed "s/my/Hao Chen's/g" pets.txt
		
		注意：如果你要使用单引号，那么你没办法通过\’这样来转义
		
		就有双引号就可以了，在双引号内可以用\”来转义。
		
		再注意：上面的sed并没有对文件的内容改变
		
		只是把处理过后的内容输出，如果你要写回文件，你可以使用重定向，如：
		
		sed"s/my/Hao Chen's/g"pets.txt > hao_pets.txt
		
		或使用 -i 参数直接修改文件内容
		
		sed -i "s/my/Hao Chen's/g" pets.txt
		
		在每一行最前面加点东西：
		
		sed 's/^/#/g' pets.txt
		
		#This is my cat
		#  my cat's name is betty
		#This is my dog
		#  my dog's name is frank
		#This is my fish
		#  my fish's name is george
		#This is my goat
		#  my goat's name is adam
		
		在每一行最后面加点东西：
		
		$ sed 's/$/ --- /g' pets.txt
		This is my cat ---
		my cat's name is betty ---
		This is my dog ---
		my dog's name is frank ---
		This is my fish ---
		my fish's name is george ---
		This is my goat ---
		my goat's name is adam ---
		
		
	
- 正则表达式

		
		最基本的东西
		
		^ 表示一行的开头。如：/^#/ 以#开头的匹配
		
		$ 表示一行的结尾。如：/}$/ 以}结尾的匹配
		
		\< 表示词首。 如 \<abc 表示以 abc 为首的詞
		
		\> 表示词尾。 如 abc\> 表示以 abc 結尾的詞
		
		. 表示任何单个字符
		
		* 表示某个字符出现了0次或多次
		
		[ ] 字符集合
		
		[abc]表示匹配a或b或c，还有[a-zA-Z]表示匹配所有的26个字符
		 
		如果其中有^表示反，如[^a]表示非a的字符
		
		

- 1

		
		要去掉某html中的tags
		
		html.txt
		
		< code>b<This</>b< is what < code>spanstyle="text-decoration: underline;"<I</>span< meant. Understand?
		
		sed命令
		
		# 如果你这样搞的话，就会有问题
		
		sed 's/< >//g' html.txt  // 是有的问题
		
		# 要解决上面的那个问题，就得像下面这样
		
		# 其中的'[^<]' 指定了除了<的字符重复0次或多次
		
		sed 's/<[^>]*>//g' html.txt
		
		结果:
		
		This is what I meant. Understand?
		
		
		
		
		
		
		
		
	

- 多个匹配

			
		如果我们需要一次替换多个模式
		
		可参看下面的示例：（第一个模式把第一行到第三行的my替换成your，第二个则把第3行以后的This替换成了That）
		
		sed '1,3s/my/your/g; 3,$s/This/That/g'my.txt
		
		This is your cat, your cat's name is betty
		This is your dog, your dog's name is frank
		That is your fish, your fish's name is george
		That is my goat, my goat's name is adam
		
		上面的命令等价于：（注：下面使用的是sed的-e命令行参数）
		
		Sed -e  '1,3s/my/your/g' -e  '3,$s/This/That/g' my.txt
			
		我们可以使用&来当做被匹配的变量，然后可以在基本左右加点东西
		
		如下所示
		
		sed 's/my/[&]/g' my.txt
		
		This is [my] cat, [my] cat's name is betty
		This is [my] dog, [my] dog's name is frank
		This is [my] fish, [my] fish's name is george
		This is [my] goat, [my] goat's name is adam
			
			
			
	
- 圆括号匹配

		
		使用圆括号匹配的示例：（圆括号括起来的正则表达式所匹配的字符串会可以当成变量来使用，sed中使用的是\1,\2…）
		
		sed 's/This is my \([^,]*\),.*is \(.*\)/\1:\2/g' my.txt
		
		cat:betty
		dog:frank
		fish:george
		goat:adam
		
		上面这个例子中的正则表达式有点复杂，解开如下（去掉转义字符）
		
		正则为：This is my ([^,]*),.*is (.*)
		
		匹配为：This is my (cat),……….is (betty)
		
		然后：\1就是cat，\2就是betty
		
		
	
- N命令

		
		N命令 —— 把下一行的内容纳入当成缓冲区做匹配
		
		下面的的示例会把原文本中的偶数行纳入奇数行匹配，而s只匹配并替换一次
		
		所以，就成了下面的结果
		
		sed'N;s/my/your/'pets.txt
		
		This is your cat
		my cat's name is betty
		This is your dog
		my dog's name is frank
		This is your fish
		my fish's name is george
		This is your goat
		my goat's name is adam
		
		也就是说，原来的文件成了：
		
		This is my cat\n  my cat's name is betty
		This is my dog\n  my dog's name is frank
		This is my fish\n  my fish's name is george
		This is my goat\n  my goat's name is adam
		
		这样一来，下面的例子你就明白了
		
		sed'N;s/\n/,/'pets.txt
		
		This is my cat,  my cat's name is betty
		This is my dog,  my dog's name is frank
		This is my fish,  my fish's name is george
		This is my goat,  my goat's name is adam
		
		
		
		
	
	
- a命令和i命令
	
			
		a命令就是append， i命令就是insert，它们是用来添加行的
		
		# 其中的1i表明，其要在第1行前插入一行（insert）
		
		sed "1 i This is my monkey, my monkey's name is wukong"my.txt
		
		This is my monkey, my monkey's name is wukong
		This is my cat, my cat's name is betty
		This is my dog, my dog's name is frank
		This is my fish, my fish's name is george
		This is my goat, my goat's name is adam
		
		# 其中的1a表明，其要在最后一行后追加一行（append）
		
		sed"$ a This is my monkey, my monkey's name is wukong"my.txt
		
		This is my cat, my cat's name is betty
		This is my monkey, my monkey's name is wukong
		This is my dog, my dog's name is frank
		This is my fish, my fish's name is george
		This is my goat, my goat's name is adam
		
		# 注意其中的/fish/a，这意思是匹配到/fish/后就追加一行
		
		sed"/fish/a This is my monkey, my monkey's name is wukong"my.txt
		
		# 对每一行都挺插入
		
		sed"/my/a ----"my.txt
		
			
			
			
	
	
- c命令

		
		c 命令是替换匹配行
		
		sed "2 c This is my monkey, my monkey's name is wukong" my.txt
		
		
		

- d命令

		
		除匹配行
		
		$ sed'/fish/d'my.txt
		
		

- p命令

		
		打印命令
		
		你可以把这个命令当成grep式的命令
		
		# 匹配fish并输出，可以看到fish的那一行被打了两遍，
		
		# 这是因为sed处理时会把处理的信息输出
		
		sed'/fish/p'my.txt
		
		# 使用n参数就好了
		
		sed -n '/fish/p'my.txt
		
		# 从一个模式到另一个模式
		
		sed -n '/dog/,/fish/p'my.txt
		
		#从第一行打印到匹配fish成功的那一行
		
		sed-n '1,/fish/p'my.txt
		
		
