# dh
<!DOCTYPE html> 
<html lang="zh-CN"> 
<head> 
    <meta charset="UTF-8"> 
    <title>智能书签管家</title> 
    <style> 
        :root { 
            --primary: #5e72e4; 
            --secondary: #2dce89; 
            --danger: #f5365c; 
            --text: #525f7f; 
            --bg: #f8f9fe; 
        } 
 
        * { 
            margin: 0; 
            padding: 0; 
            box-sizing: border-box; 
            font-family: 'Segoe UI', system-ui; 
        } 
 
        body { 
            background: var(--bg); 
            min-height: 100vh; 
            padding: 2rem; 
        } 
 
        .container { 
            max-width: 1200px; 
            margin: 0 auto; 
        } 
 
        /* 头部设计 */ 
        .header { 
            background: linear-gradient(45deg, var(--primary), #825ee4); 
            color: white; 
            padding: 2.5rem; 
            border-radius: 1rem; 
            margin-bottom: 2rem; 
            box-shadow: 0 10px 30px rgba(94,114,228,.15); 
            transition: transform 0.3s; 
        } 
        .header:hover { 
            transform: translateY(-2px); 
        } 
 
        /* 搜索栏 */ 
        .search-box { 
            background: white; 
            padding: 1rem; 
            border-radius: 0.5rem; 
            margin-bottom: 2rem; 
            display: flex; 
            gap: 1rem; 
            box-shadow: 0 2px 15px rgba(34,34,34,.05); 
        } 
        .search-input { 
            flex: 1; 
            border: none; 
            padding: 0.8rem; 
            font-size: 1.1rem; 
            background: #f8f9fe; 
            border-radius: 0.3rem; 
        } 
 
        /* 书签网格 */ 
        .grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); 
            gap: 1.5rem; 
            margin-bottom: 2rem; 
        } 
 
        /* 书签卡片 */ 
        .card { 
            background: white; 
            border-radius: 0.7rem; 
            padding: 1.5rem; 
            position: relative; 
            transition: all 0.3s cubic-bezier(0.4,0,0.2,1); 
            cursor: pointer; 
            overflow: hidden; 
        } 
        .card:hover { 
            transform: translateY(-5px); 
            box-shadow: 0 10px 20px rgba(82,95,127,.1); 
        } 
        .card::before { 
            content: ''; 
            position: absolute; 
            left: 0; 
            top: 0; 
            height: 4px; 
            width: 100%; 
            background: var(--tag-color, var(--primary)); 
        } 
        .delete-btn { 
            position: absolute; 
            top: 1rem; 
            right: 1rem; 
            width: 28px; 
            height: 28px; 
            border-radius: 50%; 
            background: rgba(245,54,92,.1); 
            display: flex; 
            align-items: center; 
            justify-content: center; 
            opacity: 0; 
            transition: all 0.3s; 
        } 
        .card:hover .delete-btn { 
            opacity: 1; 
        } 
        .delete-btn:hover { 
            background: var(--danger); 
            color: white; 
        } 
 
        /* 表单区域 */ 
        .form-panel { 
            background: white; 
            border-radius: 1rem; 
            padding: 2rem; 
            box-shadow: 0 5px 20px rgba(34,34,34,.05); 
        } 
        .form-grid { 
            display: grid; 
            grid-template-columns: 1fr 1fr auto; 
            gap: 1rem; 
            align-items: end; 
        } 
        .form-control { 
            border: 1px solid #e9ecef; 
            padding: 0.8rem; 
            border-radius: 0.5rem; 
            width: 100%; 
        } 
        .submit-btn { 
            background: var(--primary); 
            color: white; 
            padding: 0.8rem 1.5rem; 
            border: none; 
            border-radius: 0.5rem; 
            cursor: pointer; 
            transition: background 0.3s; 
        } 
        .submit-btn:hover { 
            background: #4a5cbf; 
        } 
    </style> 
</head> 
<body> 
    <div class="container"> 
        <header class="header"> 
            <h1>📚 智能书签管家</h1> 
            <p>您的高效网页导航专家</p> 
        </header> 
 
        <div class="search-box"> 
            <input type="text" class="search-input" placeholder="🔍 输入关键词搜索..." id="searchInput"> 
        </div> 
 
        <div class="grid" id="bookmarkGrid"></div> 
 
        <div class="form-panel"> 
            <div class="form-grid"> 
                <div> 
                    <label>网站名称</label> 
                    <input type="text" class="form-control" id="nameInput"> 
                </div> 
                <div> 
                    <label>网站地址</label> 
                    <input type="url" class="form-control" id="urlInput"> 
                </div> 
                <button class="submit-btn" onclick="addBookmark()">+ 添加书签</button> 
            </div> 
        </div> 
    </div> 
 
    <script> 
        let bookmarks = JSON.parse(localStorage.getItem('bookmarks'))   || []; 
        const TAG_COLORS = ['#5e72e4', '#2dce89', '#fb6340', '#f5365c', '#11cdef']; 
 
        // 渲染书签 
        function renderBookmarks(filtered = bookmarks) { 
            const grid = document.getElementById('bookmarkGrid');  
            grid.innerHTML   = filtered.map((item,   index) => ` 
                <div class="card" 
                     style="--tag-color: ${item.color}"   
                     onclick="window.open('${item.url}')">  
                    <div class="delete-btn" onclick="deleteBookmark(${index})">×</div> 
                    <h3 style="margin-bottom:0.5rem">${item.name}</h3>  
                    <p style="color:#8898aa;font-size:0.9em">${new URL(item.url).hostname}</p>  
                </div> 
            `).join(''); 
        } 
 
        // 添加书签 
        function addBookmark() { 
            const name = document.getElementById('nameInput').value.trim();  
            const url = document.getElementById('urlInput').value.trim();  
            
            if (!name || !url) return alert('请填写完整信息'); 
            
            bookmarks.push({  
                name, 
                url: url.startsWith('http')   ? url : `https://${url}`, 
                color: TAG_COLORS[bookmarks.length % TAG_COLORS.length]  
            }); 
 
            localStorage.setItem('bookmarks',   JSON.stringify(bookmarks));  
            renderBookmarks(); 
            document.getElementById('nameInput').value   = ''; 
            document.getElementById('urlInput').value   = ''; 
        } 
 
        // 删除书签 
        function deleteBookmark(index) { 
            if (!confirm('确定要删除这个书签吗？')) return; 
            bookmarks.splice(index,   1); 
            localStorage.setItem('bookmarks',   JSON.stringify(bookmarks));  
            renderBookmarks(); 
        } 
 
        // 搜索功能 
        document.getElementById('searchInput').addEventListener('input',   (e) => { 
            const term = e.target.value.toLowerCase();  
            const filtered = bookmarks.filter(item   => 
                item.name.toLowerCase().includes(term)   || 
                item.url.toLowerCase().includes(term)  
            ); 
            renderBookmarks(filtered); 
        }); 
 
        // 初始化 
        renderBookmarks(); 
    </script> 
</body> 
</html> 
