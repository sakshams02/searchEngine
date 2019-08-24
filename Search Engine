########## My Search Engine ##########

# getting the page
def get_page(url):
  try:
    import urllib
    return urllib.urlopen(url).read()
  except:
    return ''

#intermediate procedure to get next link, one after the other
def get_next_target(page):
  start_link = page.find('<a href=')
  if start_link == -1: 
    return None, 0
  start_quote = page.find('"', start_link)
  end_quote = page.find('"', start_quote + 1)
  url = page[start_quote + 1:end_quote]
  return url, end_quote

# getting all links on a page
def get_all_links(page):
  links = []
  while True:
    url,endpos = get_next_target(page)
    if url:
        links.append(url)
        page = page[endpos:]
    else:
        break
  return links

# merging the search content
def union(p,q):
  for e in q:
    if e not in p:
      p.append(e)

# adding a keyword to a datatype called index
def add_to_index(index, keyword, url):
  if keyword in index:
    index[keyword].append(url)
  else:
    index[keyword] = [url]

# making index from a page and its content
def add_page_to_index(index, url, content):
  words = content.split()
  for word in words:
    add_to_index(index, word, url)

# computing page ranks ; S_Rank Algorithm
def compute_rank(graph):
  d = 0.8                                     # damping constant
  no_loops = 10                               # no. of loop runs
  ranks = {}
  no_pages = len(graph)
  for page in graph:
    ranks[page] = 1.0/no_pages
  for i in range(0,no_loops):
    new_ranks = {}
    for page in graph:
      newrank = (1-d)/no_pages
      for node in graph:
        if page in graph[node]:
          newrank+=d*(ranks[node]/len(graph[node]))
      new_ranks[page] = newrank
    ranks = new_ranks
  return ranks

# recording number of clicks for a keyword
'''def record_user_click(index, keyword, url):
  urls = lookup(index, keyword)
  if urls:
    for entry in urls:
      if entry[0] == url:
        entry[1] = entry[1]+1'''

# search for all urls for a particular keyword
def lookup(index,keyword):
  if keyword in index:
    return index[keyword]
  return None

# enhanced lookup procedure that returns popular urls
'''def lucky_search(index, ranks, keyword):
  pages = lookup(index,keyword)
  if not pages:
    return None
  best_page = pages[0]
  for candidate in pages:
    if ranks[candidate]>ranks[best_page]:
      best_page=candidate
  return best_page'''

# tool used in ordered_search ; sort pages according to ranks
def quicksort_pages(pages,ranks):
  if not pages or len(pages)<=1:
    return pages
  else:
    pivot = ranks[pages[0]] # find pivot
    better=[]
    worse=[]
    for page in pages[1:]:
      if ranks[page]<=pivot:
        worse.append(page)
      else:
        better.append(page)
    return quicksort_pages(better,ranks) + [pages[0]] + quicksort_pages(worse,ranks)

# the best lookup modification to return ordered list of urls
def ordered_search(index, ranks, keyword):
  pages = lookup(index,keyword)
  return quicksort_pages(pages,ranks)

# web crawling
def crawl_web(seed):
  tocrawl = [seed]
  crawled = []
  index = {}
  graph = {}                                #S_Rank(Page ranking algorithm)
  while tocrawl:
    page = tocrawl.pop()
    if page not in crawled:
      content = get_page(page)
      add_page_to_index(index,page,content)
      outlinks = get_all_links(content)
      graph[page] = outlinks
      union(tocrawl,outlinks)
      crawled.append(page)
  return index, graph
