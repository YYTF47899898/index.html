<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Library Management System (Vanilla JS)</title>
  <style>
    :root{
      --bg:#f6f7fb; --card:#ffffff; --muted:#6b7280; --accent:#4f46e5; --success:#16a34a; --danger:#ef4444;
      --radius:10px; --pad:14px; --maxw:1100px;
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    body{margin:0;background:var(--bg);color:#111827}
    .wrap{max-width:var(--maxw);margin:28px auto;padding:18px}
    header{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px}
    h1{font-size:22px;margin:0}
    nav{display:flex;gap:8px}
    .btn{padding:8px 12px;border-radius:8px;border:1px solid #e6e7ee;background:white;cursor:pointer;font-size:14px}
    .btn.active{background:var(--accent);color:#fff;border-color:var(--accent)}
    .grid{display:grid;gap:14px}
    .grid.cols-3{grid-template-columns:repeat(3,1fr)}
    .card{background:var(--card);padding:var(--pad);border-radius:var(--radius);box-shadow:0 6px 18px rgba(16,24,40,0.04)}
    .card .title{color:var(--muted);font-size:13px}
    .card .num{font-size:22px;font-weight:700;margin-top:6px}
    .layout{display:flex;gap:14px;margin-top:18px}
    .left{flex:1}
    .right{width:360px}
    .panel{background:var(--card);padding:var(--pad);border-radius:var(--radius);box-shadow:0 6px 18px rgba(16,24,40,0.04)}
    .controls{display:flex;gap:8px;align-items:center}
    input[type="text"], input[type="email"], input[type="tel"], select {padding:8px 10px;border-radius:8px;border:1px solid #e6e7ee;font-size:14px;width:100%}
    table{width:100%;border-collapse:collapse}
    th, td{padding:10px 8px;text-align:left;border-bottom:1px solid #f1f3f8;font-size:14px}
    th{background:#fafafa}
    .actions a{margin-right:10px;color:var(--accent);text-decoration:none;font-size:13px;cursor:pointer}
    .muted{color:var(--muted);font-size:13px}
    .footer{margin-top:20px;text-align:center;color:var(--muted);font-size:13px}
    .notify{position:fixed;right:18px;bottom:18px;background:#fff;padding:10px 14px;border-radius:10px;box-shadow:0 6px 18px rgba(0,0,0,0.08)}
    .form-row{display:grid;grid-template-columns:repeat(6,1fr);gap:8px}
    .form-row .col-2{grid-column:span 2}
    .form-row .col-1{grid-column:span 1}
    .form-actions{display:flex;gap:8px;margin-top:8px}
    .btn-primary{background:var(--accent);color:white;border:none}
    .btn-outline{background:white;border:1px solid #e6e7ee}
    @media (max-width:900px){
      .layout{flex-direction:column}
      .right{width:100%}
      .grid.cols-3{grid-template-columns:1fr}
      .form-row{grid-template-columns:repeat(2,1fr)}
      .form-row .col-2{grid-column:span 2}
    }
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <h1>Library Management System</h1>
      <nav>
        <button class="btn active" id="nav-dashboard">Dashboard</button>
        <button class="btn" id="nav-books">Books</button>
        <button class="btn" id="nav-members">Members</button>
        <button class="btn" id="nav-issue">Issue / Return</button>
      </nav>
    </header>

    <div id="notification-placeholder"></div>

    <!-- Dashboard -->
    <div id="view-dashboard" class="view">
      <section class="grid cols-3" style="margin-bottom:16px">
        <div class="card">
          <div class="title">Books</div>
          <div class="num" id="stat-books">0</div>
          <div class="muted">Total titles stored</div>
        </div>
        <div class="card">
          <div class="title">Members</div>
          <div class="num" id="stat-members">0</div>
          <div class="muted">Registered library members</div>
        </div>
        <div class="card">
          <div class="title">Currently Issued</div>
          <div class="num" id="stat-issued">0</div>
          <div class="muted">Active issued copies</div>
        </div>
      </section>
    </div>

    <!-- Books view -->
    <div id="view-books" class="view" style="display:none">
      <div class="panel" style="margin-bottom:12px">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
          <h2 style="margin:0">Books</h2>
          <div style="display:flex;gap:8px;align-items:center">
            <input type="text" id="search-books" placeholder="Search books..." />
            <button class="btn btn-outline" id="export-books">Export CSV</button>
            <button class="btn btn-primary" id="btn-new-book">+ New</button>
          </div>
        </div>

        <form id="book-form">
          <div class="form-row" style="align-items:center">
            <input class="col-2" type="text" placeholder="Title" id="book-title" required />
            <input class="col-1" type="text" placeholder="Author" id="book-author" />
            <input class="col-1" type="text" placeholder="ISBN" id="book-isbn" />
            <input class="col-1" type="number" min="1" placeholder="Copies" id="book-copies" value="1" />
            <input class="col-1" type="text" placeholder="Category" id="book-category" />
            <input type="hidden" id="book-id" />
          </div>
          <div class="form-actions">
            <button class="btn btn-primary" type="submit">Save</button>
            <button type="button" class="btn btn-outline" id="book-clear">Clear</button>
          </div>
        </form>
      </div>

      <div class="panel">
        <div style="overflow-x:auto">
          <table id="books-table">
            <thead>
              <tr><th>ID</th><th>Title</th><th>Author</th><th>Copies</th><th>Available</th><th>Actions</th></tr>
            </thead>
            <tbody></tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- Members view -->
    <div id="view-members" class="view" style="display:none">
      <div class="panel" style="margin-bottom:12px">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
          <h2 style="margin:0">Members</h2>
          <div style="display:flex;gap:8px;align-items:center">
            <button class="btn btn-outline" id="export-members">Export CSV</button>
            <button class="btn btn-primary" id="btn-new-member">+ New</button>
          </div>
        </div>

        <form id="member-form">
          <div class="form-row" style="align-items:center">
            <input class="col-2" type="text" placeholder="Name" id="member-name" required />
            <input class="col-2" type="email" placeholder="Email" id="member-email" />
            <input class="col-1" type="tel" placeholder="Phone" id="member-phone" />
            <input type="hidden" id="member-id" />
          </div>
          <div class="form-actions">
            <button class="btn btn-primary" type="submit">Save</button>
            <button type="button" class="btn btn-outline" id="member-clear">Clear</button>
          </div>
        </form>
      </div>

      <div class="panel">
        <div style="overflow-x:auto">
          <table id="members-table">
            <thead>
              <tr><th>ID</th><th>Name</th><th>Email</th><th>Phone</th><th>Actions</th></tr>
            </thead>
            <tbody></tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- Issue view -->
    <div id="view-issue" class="view" style="display:none">
      <div class="layout">
        <div class="left">
          <div class="panel">
            <h3 style="margin-top:0">Issue / Return</h3>
            <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px">
              <div>
                <label class="muted">Select Book</label>
                <select id="issue-book"></select>
              </div>
              <div>
                <label class="muted">Select Member</label>
                <select id="issue-member"></select>
              </div>
              <div style="grid-column:span 2;display:flex;gap:8px;justify-content:flex-end">
                <button class="btn btn-primary" id="issue-14">Issue 14d</button>
                <button class="btn btn-outline" id="issue-7">Issue 7d</button>
              </div>
            </div>
          </div>

          <div style="height:12px"></div>

          <div class="panel">
            <h4 style="margin-top:0">Active Issues</h4>
            <div style="overflow-x:auto">
              <table id="issues-table">
                <thead><tr><th>Txn ID</th><th>Book</th><th>Member</th><th>Due</th><th>Actions</th></tr></thead>
                <tbody></tbody>
              </table>
            </div>
          </div>
        </div>

        <aside class="right">
          <div class="panel">
            <h4 style="margin-top:0">Quick Stats</h4>
            <div><strong id="quick-books">0</strong> Books</div>
            <div><strong id="quick-members">0</strong> Members</div>
            <div><strong id="quick-issued">0</strong> Issued</div>
          </div>
        </aside>
      </div>
    </div>

    <div class="footer">Made by <b>Rajat Singh</b> • Simple LMS • Data stored securely</div>
  </div>

  <div id="toast" class="notify" style="display:none"></div>

  <script>
    // --- storage keys and seed data ---
    const KEY_BOOKS = 'lms_books';
    const KEY_MEMBERS = 'lms_members';
    const KEY_TX = 'lms_transactions';

    function seedBooks(){ return [
      { id: 'B_ABC1234', title: 'Introduction to Algorithms', author: 'Cormen, Leiserson et al.', isbn: '0262033844', copies: 3, category: 'Algorithms' },
      { id: 'B_DEF5678', title: 'Clean Code', author: 'Robert C. Martin', isbn: '0132350882', copies: 2, category: 'Software Engineering' },
      { id: 'B_GHI9012', title: 'Discrete Mathematics', author: 'Rosen', isbn: '0073383090', copies: 2, category: 'Mathematics' },
    ];}
    function seedMembers(){ return [
      { id: 'M_111AAAA', name: 'Rajat Singh', email: 'rajat@example.com', phone: '9999999999' },
      { id: 'M_222BBBB', name: 'Anita Sharma', email: 'anita@example.com', phone: '8888888888' },
    ];}

    // read/write helpers
    function read(key, fallback){ try{ const r = localStorage.getItem(key); return r ? JSON.parse(r) : fallback; }catch(e){console.error(e); return fallback;} }
    function write(key, value){ try{ localStorage.setItem(key, JSON.stringify(value)); }catch(e){console.error(e);} }

    // in-memory state
    let books = read(KEY_BOOKS, seedBooks());
    let members = read(KEY_MEMBERS, seedMembers());
    let transactions = read(KEY_TX, []);

    // UI elements
    const views = {
      dashboard: document.getElementById('view-dashboard'),
      books: document.getElementById('view-books'),
      members: document.getElementById('view-members'),
      issue: document.getElementById('view-issue')
    };
    const navButtons = {
      dashboard: document.getElementById('nav-dashboard'),
      books: document.getElementById('nav-books'),
      members: document.getElementById('nav-members'),
      issue: document.getElementById('nav-issue'),
    };

    // forms & tables
    const booksTableBody = document.querySelector('#books-table tbody');
    const membersTableBody = document.querySelector('#members-table tbody');
    const issuesTableBody = document.querySelector('#issues-table tbody');
    const searchBooksInput = document.getElementById('search-books');

    // book form
    const bookForm = document.getElementById('book-form');
    const bookTitle = document.getElementById('book-title');
    const bookAuthor = document.getElementById('book-author');
    const bookISBN = document.getElementById('book-isbn');
    const bookCopies = document.getElementById('book-copies');
    const bookCategory = document.getElementById('book-category');
    const bookIdInput = document.getElementById('book-id');
    const bookClearBtn = document.getElementById('book-clear');
    const btnNewBook = document.getElementById('btn-new-book');
    const exportBooksBtn = document.getElementById('export-books');

    // member form
    const memberForm = document.getElementById('member-form');
    const memberName = document.getElementById('member-name');
    const memberEmail = document.getElementById('member-email');
    const memberPhone = document.getElementById('member-phone');
    const memberIdInput = document.getElementById('member-id');
    const memberClearBtn = document.getElementById('member-clear');
    const btnNewMember = document.getElementById('btn-new-member');
    const exportMembersBtn = document.getElementById('export-members');

    // issue controls
    const issueBookSelect = document.getElementById('issue-book');
    const issueMemberSelect = document.getElementById('issue-member');
    const issue14Btn = document.getElementById('issue-14');
    const issue7Btn = document.getElementById('issue-7');

    // stats
    const statBooks = document.getElementById('stat-books');
    const statMembers = document.getElementById('stat-members');
    const statIssued = document.getElementById('stat-issued');
    const quickBooks = document.getElementById('quick-books');
    const quickMembers = document.getElementById('quick-members');
    const quickIssued = document.getElementById('quick-issued');

    // toast
    const toast = document.getElementById('toast');
    let toastTimer = null;
    function notify(msg){
      toast.textContent = msg;
      toast.style.display = 'block';
      clearTimeout(toastTimer);
      toastTimer = setTimeout(()=> toast.style.display = 'none', 3000);
    }

    // helper id generator
    function genId(prefix='X'){ return prefix + '_' + Math.random().toString(36).slice(2,9).toUpperCase(); }
    function addDays(date, days){ const d = new Date(date); d.setDate(d.getDate() + days); return d; }

    // available copies
    function getAvailableCopies(bookId){
      const b = books.find(x => x.id === bookId);
      if(!b) return 0;
      const issued = transactions.filter(t => t.bookId === bookId && t.type === 'issue' && !t.returned).length;
      return Math.max(0, Number(b.copies||0) - issued);
    }

    // render functions
    function renderStats(){
      statBooks.textContent = books.length;
      statMembers.textContent = members.length;
      statIssued.textContent = transactions.filter(t=>t.type==='issue' && !t.returned).length;
      quickBooks.textContent = books.length;
      quickMembers.textContent = members.length;
      quickIssued.textContent = transactions.filter(t=>t.type==='issue' && !t.returned).length;
    }

    function renderBooks(filter=''){
      booksTableBody.innerHTML = '';
      const q = (filter||'').trim().toLowerCase();
      const list = books.filter(b => {
        if(!q) return true;
        return [b.title,b.author,b.isbn,b.category,b.id].some(f => (f||'').toString().toLowerCase().includes(q));
      });
      list.forEach(b => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${b.id}</td>
          <td>${escapeHtml(b.title)}</td>
          <td>${escapeHtml(b.author||'')}</td>
          <td>${b.copies}</td>
          <td>${getAvailableCopies(b.id)}</td>
          <td class="actions">
            <a data-action="edit" data-id="${b.id}">Edit</a>
            <a data-action="issue" data-id="${b.id}">Issue</a>
            <a data-action="delete" data-id="${b.id}" style="color:${'var(--danger)'}">Delete</a>
          </td>
        `;
        booksTableBody.appendChild(tr);
      });
      populateIssueBookSelect();
    }

    function renderMembers(){
      membersTableBody.innerHTML = '';
      members.forEach(m => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${m.id}</td>
          <td>${escapeHtml(m.name)}</td>
          <td>${escapeHtml(m.email||'')}</td>
          <td>${escapeHtml(m.phone||'')}</td>
          <td class="actions">
            <a data-action="edit" data-id="${m.id}">Edit</a>
            <a data-action="delete" data-id="${m.id}" style="color:${'var(--danger)'}">Delete</a>
          </td>
        `;
        membersTableBody.appendChild(tr);
      });
      populateIssueMemberSelect();
    }

    function renderIssues(){
      issuesTableBody.innerHTML = '';
      const active = transactions.filter(t => t.type==='issue' && !t.returned);
      active.forEach(t => {
        const b = books.find(x=>x.id===t.bookId) || {title:t.bookId};
        const m = members.find(x=>x.id===t.memberId) || {name:t.memberId};
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${t.id}</td>
          <td>${escapeHtml(b.title)}</td>
          <td>${escapeHtml(m.name)}</td>
          <td>${new Date(t.dueDate).toLocaleDateString()}</td>
          <td class="actions"><a data-action="return" data-id="${t.id}">Return</a></td>
        `;
        issuesTableBody.appendChild(tr);
      });
    }

    function populateIssueBookSelect(){
      issueBookSelect.innerHTML = '<option value="">-- choose --</option>';
      books.forEach(b => {
        const opt = document.createElement('option');
        opt.value = b.id;
        opt.textContent = `${b.title} — ${b.id} (Avail: ${getAvailableCopies(b.id)})`;
        issueBookSelect.appendChild(opt);
      });
    }
    function populateIssueMemberSelect(){
      issueMemberSelect.innerHTML = '<option value="">-- choose --</option>';
      members.forEach(m => {
        const opt = document.createElement('option');
        opt.value = m.id;
        opt.textContent = `${m.name} — ${m.id}`;
        issueMemberSelect.appendChild(opt);
      });
    }

    // helpers
    function escapeHtml(s){ return (s||'').toString().replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;'); }

    // save backend
    function persistAll(){
      write(KEY_BOOKS, books);
      write(KEY_MEMBERS, members);
      write(KEY_TX, transactions);
      renderAll();
    }

    // render wrapper
    function renderAll(){
      renderStats();
      renderBooks(searchBooksInput.value || '');
      renderMembers();
      renderIssues();
    }

    // actions: books
    bookForm.addEventListener('submit', function(ev){
      ev.preventDefault();
      const id = bookIdInput.value.trim();
      const obj = {
        id: id || genId('B'),
        title: bookTitle.value.trim(),
        author: bookAuthor.value.trim(),
        isbn: bookISBN.value.trim(),
        copies: Number(bookCopies.value) || 1,
        category: bookCategory.value.trim()
      };
      if(!obj.title){ alert('Book title required'); return; }
      if(id){
        books = books.map(b => b.id===id ? obj : b);
        notify('Book updated');
      } else {
        books.unshift(obj);
        notify('Book added');
      }
      bookForm.reset();
      bookIdInput.value = '';
      persistAll();
    });

    bookClearBtn.addEventListener('click', ()=>{ bookForm.reset(); bookIdInput.value=''; });

    btnNewBook.addEventListener('click', ()=>{
      // scroll into view (not necessary in static file but helpful)
      window.location.hash = '';
      bookForm.scrollIntoView({behavior:'smooth'});
      bookForm.reset();
      bookIdInput.value='';
    });

    // books table actions (delegate)
    booksTableBody.addEventListener('click', function(e){
      const a = e.target.closest('a');
      if(!a) return;
      const action = a.dataset.action;
      const id = a.dataset.id;
      if(action==='edit'){
        const b = books.find(x=>x.id===id);
        if(!b) return;
        bookIdInput.value = b.id;
        bookTitle.value = b.title;
        bookAuthor.value = b.author;
        bookISBN.value = b.isbn;
        bookCopies.value = b.copies;
        bookCategory.value = b.category;
        // switch to books view if not
        showView('books');
        bookForm.scrollIntoView({behavior:'smooth'});
      } else if(action==='delete'){
        if(!confirm('Delete this book?')) return;
        books = books.filter(x=>x.id!==id);
        transactions = transactions.filter(t=>t.bookId!==id);
        persistAll();
        notify('Book deleted');
      } else if(action==='issue'){
        const b = books.find(x=>x.id===id);
        if(!b) return;
        issueBookSelect.value = id;
        showView('issue');
      }
    });

    // search books
    searchBooksInput.addEventListener('input', ()=> renderBooks(searchBooksInput.value));

    // export CSV
    function exportCSV(list, filename='export.csv'){
      if(!list || !list.length){ alert('Nothing to export'); return; }
      const keys = Object.keys(list[0]);
      const rows = [keys.join(','), ...list.map(obj => keys.map(k => `"${(obj[k] ?? '')}"`).join(','))].join('\\n');
      const blob = new Blob([rows], {type:'text/csv'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url; a.download = filename; a.click();
      URL.revokeObjectURL(url);
    }
    exportBooksBtn.addEventListener('click', ()=> exportCSV(books, 'books.csv'));
    exportMembersBtn.addEventListener('click', ()=> exportCSV(members, 'members.csv'));

    // members CRUD
    memberForm.addEventListener('submit', function(e){
      e.preventDefault();
      const id = memberIdInput.value.trim();
      const obj = {
        id: id || genId('M'),
        name: memberName.value.trim(),
        email: memberEmail.value.trim(),
        phone: memberPhone.value.trim()
      };
      if(!obj.name){ alert('Member name required'); return; }
      if(id){
        members = members.map(m => m.id===id ? obj : m);
        notify('Member updated');
      } else {
        members.unshift(obj);
        notify('Member added');
      }
      memberForm.reset();
      memberIdInput.value='';
      persistAll();
    });
    memberClearBtn.addEventListener('click', ()=>{ memberForm.reset(); memberIdInput.value=''; });
    btnNewMember.addEventListener('click', ()=>{ memberForm.reset(); memberIdInput.value=''; memberForm.scrollIntoView({behavior:'smooth'}); });

    membersTableBody.addEventListener('click', function(e){
      const a = e.target.closest('a');
      if(!a) return;
      const action = a.dataset.action;
      const id = a.dataset.id;
      if(action==='edit'){
        const m = members.find(x=>x.id===id);
        if(!m) return;
        memberIdInput.value = m.id;
        memberName.value = m.name;
        memberEmail.value = m.email;
        memberPhone.value = m.phone;
        showView('members');
        memberForm.scrollIntoView({behavior:'smooth'});
      } else if(action==='delete'){
        if(!confirm('Delete this member? This will delete their transaction history.')) return;
        members = members.filter(x=>x.id!==id);
        transactions = transactions.filter(t=>t.memberId!==id);
        persistAll();
        notify('Member deleted');
      }
    });

    // issue & return functions
    function issueBook(bookId, memberId, days=14){
      const book = books.find(b=>b.id===bookId);
      const member = members.find(m=>m.id===memberId);
      if(!book || !member) return alert('Invalid book or member');
      if(getAvailableCopies(bookId) < 1) return alert('No available copies to issue');
      const due = addDays(new Date(), days);
      const tx = { id: genId('T'), bookId, memberId, type:'issue', date:new Date().toISOString(), dueDate: due.toISOString(), returned:false };
      transactions.unshift(tx);
      persistAll();
      notify(`Issued "${book.title}" to ${member.name}. Due ${due.toLocaleDateString()}`);
    }

    function returnBook(txId){
      const tx = transactions.find(t=>t.id===txId);
      if(!tx) return;
      if(tx.type==='issue' && !tx.returned){
        tx.returned = true;
        tx.type = 'return';
        tx.returnDate = new Date().toISOString();
        transactions = transactions.map(t => t.id===txId ? tx : t);
        persistAll();
        notify('Book returned');
      }
    }

    issue14Btn.addEventListener('click', ()=>{
      const b = issueBookSelect.value;
      const m = issueMemberSelect.value;
      if(!b || !m) return alert('Select both book and member');
      issueBook(b,m,14);
    });
    issue7Btn.addEventListener('click', ()=>{
      const b = issueBookSelect.value;
      const m = issueMemberSelect.value;
      if(!b || !m) return alert('Select both book and member');
      issueBook(b,m,7);
    });

    // issues table actions (return)
    issuesTableBody.addEventListener('click', function(e){
      const a = e.target.closest('a');
      if(!a) return;
      const action = a.dataset.action;
      const id = a.dataset.id;
      if(action==='return'){
        returnBook(id);
      }
    });

    // nav control
    function showView(name){
      for(const k in views){ views[k].style.display = (k===name ? '' : 'none'); }
      for(const k in navButtons){ navButtons[k].classList.toggle('active', k===name); }
      // re-render when view changes
      renderAll();
    }
    navButtons.dashboard.addEventListener('click', ()=> showView('dashboard'));
    navButtons.books.addEventListener('click', ()=> showView('books'));
    navButtons.members.addEventListener('click', ()=> showView('members'));
    navButtons.issue.addEventListener('click', ()=> showView('issue'));

    // initial render
    renderAll();

    // small utility: escape for CSV export is done earlier by wrapping values in quotes
    // helper to persist when user types in console to mutate arrays
    window._debug = { books, members, transactions, persistAll };

    // simple keyboard shortcuts (optional)
    document.addEventListener('keydown', (e)=>{
      if(e.ctrlKey && e.key==='b'){ showView('books'); }
      if(e.ctrlKey && e.key==='m'){ showView('members'); }
      if(e.ctrlKey && e.key==='i'){ showView('issue'); }
    });
  </script>
</body>
</html>
