import React, { useState, useEffect, useRef, useCallback, memo } from 'react';
import { initializeApp } from 'firebase/app';
import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from 'firebase/auth';
import { getFirestore, collection, addDoc, onSnapshot, query, serverTimestamp, orderBy, doc, getDoc, setDoc, deleteDoc, FieldValue, increment } from 'firebase/firestore';

// --- SVG Icons ---
const HeartIcon = ({ filled, className = "" }) => (
  <svg className={`w-6 h-6 inline-block ${className}`} fill={filled ? "currentColor" : "none"} stroke="currentColor" viewBox="0 0 20 20">
    <path fillRule="evenodd" d="M3.172 5.172a4 4 0 015.656 0L10 6.343l1.172-1.171a4 4 0 115.656 5.656L10 17.657l-6.828-6.829a4 4 0 010-5.656z" clipRule="evenodd"></path>
  </svg>
);
const AtSymbolIcon = () => (
  <svg className="w-4 h-4 inline-block mr-2 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M16 12a4 4 0 10-8 0 4 4 0 008 0zm0 0v1.5a2.5 2.5 0 005 0V12a9 9 0 10-9 9m4.5-1.206a8.959 0 01-4.5 1.207" /></svg>
);
const PlusIcon = () => (
  <svg className="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" /></svg>
);
const CloseIcon = () => (
  <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M6 18L18 6M6 6l12 12"></path></svg>
);
const SearchIcon = () => (
  <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
);
const ClearIcon = () => (
  <svg className="w-5 h-5 text-gray-500 hover:text-gray-700 cursor-pointer" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M6 18L18 6M6 6l12 12"></path></svg>
);
const ArchiveIcon = () => (
  <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 8h14M5 8a2 2 0 110-4h14a2 2 0 110 4M5 8v10a2 2 0 002 2h10a2 2 0 002-2V8m-9 4h4"></path></svg>
);
const HomeIcon = () => (
  <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m0 0l7 7m-15 4v5a1 1 0 001 1h3m-6 0h6"></path></svg>
);
const ShareIcon = () => (
  <svg className="w-5 h-5 text-gray-600 hover:text-purple-700 cursor-pointer" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M8.684 13.342C8.882 13.064 9 12.735 9 12c0-.736-.118-1.065-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.21-3.606A3 3 0 0015.5 12c0 .59-.095 1.096-.277 1.554m0-2.684l-6.21 3.606c-.182-.458-.277-.964-.277-1.554M15.5 12A3 3 0 1115.5 9a3 3 0 010 3z"></path></svg>
);
const ReplyIcon = () => (
  <svg className="w-5 h-5 text-gray-600 hover:text-blue-700 cursor-pointer" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path></svg>
);
const TrendingIcon = () => (
  <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"></path></svg>
);


// --- Reusable Components ---

const LoadingSpinner = () => (
  <div className="flex justify-center items-center p-10"><div className="w-16 h-16 border-4 border-dashed rounded-full animate-spin border-pink-500"></div></div>
);

const InfoModal = ({ message, onClose }) => (
  <div className="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center p-4 z-[100] backdrop-blur-sm">
    <div className="bg-white p-8 rounded-2xl shadow-2xl max-w-sm w-full text-center transform transition-all scale-95 hover:scale-100 duration-300">
      <p className="text-gray-800 text-xl mb-6">{message}</p>
      <button onClick={onClose} className="bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-6 rounded-full transition duration-300 ease-in-out transform hover:scale-105">Close</button>
    </div>
  </div>
);

// Navigation Bar component (Slim bar above header)
const NavBar = memo(({ onOpenSearch, onChangeView, currentView }) => (
  <nav className="w-full max-w-3xl bg-white/30 backdrop-blur-lg p-3 rounded-2xl shadow-xl mb-4 flex justify-between items-center border border-white/20">
    {/* Search Button */}
    <button
      onClick={onOpenSearch}
      className="bg-purple-500 hover:bg-purple-600 text-white p-2 rounded-full shadow-lg transition duration-300 ease-in-out transform hover:scale-110"
      aria-label="Open search"
      title="Search Confessions"
    >
      <SearchIcon />
    </button>

    {/* View Navigation Buttons */}
    <div className="flex space-x-2">
      <button
        onClick={() => onChangeView('recent')}
        className={`p-2 rounded-full shadow-lg transition duration-300 ease-in-out transform ${currentView === 'recent' ? 'bg-pink-600' : 'bg-pink-500 hover:bg-pink-600'} text-white hover:scale-110`}
        title="Show Recent Confessions"
      >
        <HomeIcon />
      </button>
      <button
        onClick={() => onChangeView('archive')}
        className={`p-2 rounded-full shadow-lg transition duration-300 ease-in-out transform ${currentView === 'archive' ? 'bg-indigo-600' : 'bg-indigo-500 hover:bg-indigo-600'} text-white hover:scale-110`}
        title="Show Archived Confessions"
      >
        <ArchiveIcon />
      </button>
      <button
        onClick={() => onChangeView('popular')}
        className={`p-2 rounded-full shadow-lg transition duration-300 ease-in-out transform ${currentView === 'popular' ? 'bg-green-600' : 'bg-green-500 hover:bg-green-600'} text-white hover:scale-110`}
        title="Show Popular Confessions"
      >
        <TrendingIcon />
      </button>
    </div>
  </nav>
));


// Main Header component
const Header = memo(({ userId, onClearSharedConfession }) => (
  <header className="bg-white/30 backdrop-blur-lg p-6 rounded-2xl shadow-xl mb-8 w-full max-w-3xl text-center border border-white/20 relative">
    <h1 className="text-5xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-purple-600 to-pink-500 mb-2">💖 The Crush Board 💖</h1>
    <p className="text-gray-700 text-lg">Browse recent confessions or post your own!</p>
    {userId && <p className="text-sm text-gray-600 mt-2 bg-white/50 rounded-full px-3 py-1 inline-block">User ID: <span className="font-mono text-xs break-all">{userId}</span></p>}

    {/* Button to clear shared confession view - shown only when a specific confession is being viewed */}
    {onClearSharedConfession && (
      <button
        onClick={onClearSharedConfession}
        className="absolute top-4 left-1/2 -translate-x-1/2 bg-blue-500 hover:bg-blue-600 text-white py-1 px-3 rounded-full shadow-lg transition duration-300 ease-in-out text-sm"
        title="View All Confessions"
      >
        View All
      </button>
    )}
  </header>
));

const ConfessionCard = memo(({ confession, formatTimestamp, onShare, onOpenReplyModal, onToggleLike, userId }) => {
  const [copied, setCopied] = useState(false);
  const [hasLiked, setHasLiked] = useState(false); // State to track if current user has liked

  // Check if current user has liked this confession based on the likedBy map
  useEffect(() => {
    if (confession.likedBy && userId) {
      setHasLiked(confession.likedBy[userId] === true);
    } else {
      setHasLiked(false);
    }
  }, [confession.likedBy, userId]);

  // Handle sharing a confession link
  const handleShareClick = () => {
    onShare(confession.id);
    setCopied(true);
    const timer = setTimeout(() => {
      setCopied(false);
    }, 2000); // Reset copied status after 2 seconds
    return () => clearTimeout(timer); // Cleanup timeout
  };

  // Handle toggling like on a confession
  const handleLikeClick = () => {
    onToggleLike(confession.id, hasLiked); // Pass confession ID and current liked status to parent
  };

  return (
    <div className="bg-white/30 backdrop-blur-lg border border-white/20 p-5 rounded-xl mb-4 shadow-lg transition-all duration-500 transform hover:scale-[1.02] hover:shadow-xl break-words animate-fade-in-up relative">
      <p className="text-gray-800 text-lg mb-3 italic">"{confession.message}"</p>
      <div className="border-t border-purple-200/50 my-3"></div>
      <div className="flex items-center text-pink-700 font-semibold mb-2"><HeartIcon />To: <span className="text-purple-800 ml-2">{confession.crushName}</span></div>
      {confession.socialMedia && (
        <div className="flex items-center text-sm text-gray-600 mb-2">
          <AtSymbolIcon />
          <a href={confession.socialMedia.startsWith('http') ? confession.socialMedia : `https://${confession.socialMedia}`} target="_blank" rel="noopener noreferrer" className="text-blue-600 hover:underline">
            {confession.socialMedia}
          </a>
        </div>
      )}
      <p className="text-xs text-gray-500 text-right mt-2">{formatTimestamp(confession.timestamp)}</p>
      <p className="text-[10px] text-gray-400 text-right mt-1">ID: <span className="font-mono">{confession.id}</span></p>

      {/* Action Buttons Container */}
      <div className="flex justify-between items-center mt-3">
        {/* Like Button and Count */}
        <div className="flex items-center space-x-1">
          <button
            onClick={handleLikeClick}
            className="p-1 rounded-full bg-white/50 hover:bg-white/80 transition duration-200 shadow-sm"
            title={hasLiked ? "Unlike" : "Like"}
          >
            <HeartIcon filled={hasLiked} className={hasLiked ? "text-red-500" : "text-gray-600"} />
          </button>
          <span className="text-sm font-semibold text-gray-700">{confession.likesCount || 0}</span>
        </div>

        <div className="flex items-center space-x-2">
          {/* Share Button */}
          <button
            onClick={handleShareClick}
            className="p-1 rounded-full bg-white/50 hover:bg-white/80 transition duration-200 shadow-sm"
            title="Share this confession"
          >
            <ShareIcon />
          </button>
          {copied && (
            <span className="text-xs text-green-700 bg-green-100 rounded-full px-2 py-1 animate-fade-in-up">Copied!</span>
          )}

          {/* Reply Button */}
          <button
            onClick={() => onOpenReplyModal(confession)}
            className="p-1 rounded-full bg-white/50 hover:bg-white/80 transition duration-200 shadow-sm"
            title="Reply to this confession"
          >
            <ReplyIcon />
          </button>
        </div>
      </div>
    </div>
  );
});

const ConfessionsList = ({ loading, confessions, formatTimestamp, confessionsEndRef, title, onShare, onOpenReplyModal, onToggleLike, userId }) => (
  <section className="w-full max-w-3xl">
    <h2 className="text-3xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-purple-500 to-pink-400 mb-6 text-center">{title}</h2>
    {loading ? <LoadingSpinner /> : confessions.length === 0 ? (
      <div className="bg-white/30 backdrop-blur-lg p-8 rounded-2xl shadow-xl text-center text-gray-700"><p>No confessions found in this section. ✨</p></div>
    ) : (
      <div className="max-h-[70vh] overflow-y-auto custom-scrollbar pr-2">
        {confessions.map((confession) => (
          <ConfessionCard
            key={confession.id}
            confession={confession}
            formatTimestamp={formatTimestamp}
            onShare={onShare}
            onOpenReplyModal={onOpenReplyModal}
            onToggleLike={onToggleLike}
            userId={userId} // Pass userId to ConfessionCard for like logic
          />
        ))}
        <div ref={confessionsEndRef} />
      </div>
    )}
  </section>
);

const ConfessionFormModal = ({ isOpen, onClose, children }) => {
  if (!isOpen) return null;
  return (
    <div className="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center p-4 z-50 backdrop-blur-sm" onClick={onClose}>
      <div className="bg-white/80 backdrop-blur-2xl p-8 rounded-2xl shadow-2xl w-full max-w-lg relative transform transition-all duration-300 scale-95 animate-modal-in" onClick={(e) => e.stopPropagation()}>
        <button onClick={onClose} className="absolute top-4 right-4 text-gray-500 hover:text-gray-800 transition"><CloseIcon /></button>
        {children}
      </div>
    </div>
  );
};

const SearchModal = ({ isOpen, onClose, searchTerm, setSearchTerm, searchCategory, setSearchCategory }) => {
  if (!isOpen) return null;
  return (
    <div className="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center p-4 z-50 backdrop-blur-sm" onClick={onClose}>
      <div className="bg-white/80 backdrop-blur-2xl p-8 rounded-2xl shadow-2xl w-full max-w-lg relative transform transition-all duration-300 scale-95 animate-modal-in" onClick={(e) => e.stopPropagation()}>
        <button onClick={onClose} className="absolute top-4 right-4 text-gray-500 hover:text-gray-800 transition"><CloseIcon /></button>
        <h2 className="text-2xl font-semibold text-gray-800 mb-6 text-center">Search Confessions</h2>
        <div className="space-y-4">
          <input
            type="text"
            placeholder="Enter search term..."
            className="w-full p-3 bg-white/80 border border-gray-300/50 rounded-lg focus:ring-2 focus:ring-purple-400 focus:border-transparent transition duration-200 placeholder-gray-500"
            value={searchTerm}
            onChange={(e) => setSearchTerm(e.target.value)}
          />
          <select
            className="w-full p-3 bg-white/80 border border-gray-300/50 rounded-lg focus:ring-2 focus:ring-pink-400 focus:border-transparent transition duration-200 text-gray-700"
            value={searchCategory}
            onChange={(e) => setSearchCategory(e.target.value)}
          >
            <option value="text">Message/Crush Name</option>
            <option value="date">Date</option>
            <option value="id">Confession ID</option>
            <option value="crushName">Crush's Name Only</option>
          </select>
          <button
            onClick={() => setSearchTerm('')} // Clear search term
            className="w-full py-3 px-6 rounded-full font-bold text-white shadow-lg transform transition duration-300 ease-in-out bg-gray-500 hover:bg-gray-600"
            aria-label="Clear search"
          >
            Clear Search
          </button>
        </div>
      </div>
    </div>
  );
};

const ReplyModal = ({ isOpen, onClose, originalConfession, db, userId, formatTimestamp, onShowInfoModal }) => {
  const [replyMessage, setReplyMessage] = useState('');
  const [replies, setReplies] = useState([]);
  const [isReplying, setIsReplying] = useState(false);
  const repliesEndRef = useRef(null);

  // Fetch replies for the current confession
  useEffect(() => {
    if (!db || !originalConfession?.id) return;

    const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
    const repliesCollectionPath = `artifacts/${appId}/public/data/crush_board/${originalConfession.id}/replies`;
    const q = query(collection(db, repliesCollectionPath), orderBy('timestamp', 'asc')); // Order replies by time

    const unsub = onSnapshot(q, (snapshot) => {
      const fetchedReplies = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      setReplies(fetchedReplies);
    }, (error) => {
      console.error("Error fetching replies:", error);
      onShowInfoModal(`Failed to load replies: ${error.message}`);
    });

    return () => unsub(); // Cleanup listener
  }, [db, originalConfession?.id, onShowInfoModal]);

  // Scroll to bottom of replies when new replies are added
  useEffect(() => {
    repliesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [replies]);

  const handlePostReply = async () => {
    if (!replyMessage.trim()) {
      onShowInfoModal("Please enter a reply message.");
      return;
    }
    if (!db || !userId || !originalConfession?.id) {
      onShowInfoModal("Cannot post reply: App not connected or original message missing.");
      return;
    }

    setIsReplying(true);
    try {
      const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
      const repliesCollectionPath = `artifacts/${appId}/public/data/crush_board/${originalConfession.id}/replies`;
      await addDoc(collection(db, repliesCollectionPath), {
        message: replyMessage.trim(),
        timestamp: serverTimestamp(),
        userId: userId,
        originalConfessionId: originalConfession.id, // Redundant but good for clarity
      });
      setReplyMessage(''); // Clear input
      setIsReplying(false);
    } catch (error) {
      console.error("Error posting reply:", error);
      onShowInfoModal(`Failed to post reply: ${error.message}.`);
      setIsReplying(false);
    }
  };

  if (!isOpen || !originalConfession) return null;

  return (
    <div className="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center p-4 z-50 backdrop-blur-sm" onClick={onClose}>
      <div className="bg-white/80 backdrop-blur-2xl p-8 rounded-2xl shadow-2xl w-full max-w-2xl h-[90vh] flex flex-col relative transform transition-all duration-300 scale-95 animate-modal-in" onClick={(e) => e.stopPropagation()}>
        <button onClick={onClose} className="absolute top-4 right-4 text-gray-500 hover:text-gray-800 transition"><CloseIcon /></button>
        <h2 className="text-2xl font-semibold text-gray-800 mb-4 text-center">Reply to Confession</h2>

        {/* Original Confession Snippet */}
        <div className="bg-purple-50 p-4 rounded-lg mb-4 border border-purple-200">
          <p className="text-gray-700 italic text-md mb-2">"{originalConfession.message}"</p>
          <p className="text-sm text-gray-500 text-right">To: {originalConfession.crushName} - {formatTimestamp(originalConfession.timestamp)}</p>
        </div>

        {/* Replies List */}
        <div className="flex-grow overflow-y-auto custom-scrollbar pr-2 mb-4 bg-gray-50 p-4 rounded-lg border border-gray-200">
          {replies.length === 0 ? (
            <p className="text-gray-500 text-center italic">No replies yet. Be the first!</p>
          ) : (
            replies.map((reply) => (
              <div key={reply.id} className="mb-3 p-3 bg-white rounded-lg shadow-sm border border-gray-100 last:mb-0 animate-fade-in-up">
                <p className="text-gray-800">{reply.message}</p>
                <p className="text-xs text-gray-500 text-right mt-1">
                  Replied by {reply.userId.substring(0, 6)}... at {formatTimestamp(reply.timestamp)}
                </p>
              </div>
            ))
          )}
          <div ref={repliesEndRef} />
        </div>

        {/* Reply Input Form */}
        <div className="mt-auto pt-4 border-t border-gray-200">
          <textarea
            className="w-full p-3 bg-white/80 border border-gray-300/50 rounded-lg focus:ring-2 focus:ring-blue-400 focus:border-transparent transition duration-200 resize-y min-h-[60px] placeholder-gray-500"
            placeholder="Type your reply here..."
            value={replyMessage}
            onChange={(e) => setReplyMessage(e.target.value)}
            rows="2"
            disabled={isReplying}
          />
          <button
            onClick={handlePostReply}
            disabled={isReplying}
            className={`w-full py-2 px-4 rounded-full font-bold text-white shadow-lg transform transition duration-300 ease-in-out mt-3 ${isReplying ? 'bg-gray-400 cursor-not-allowed' : 'bg-blue-600 hover:bg-blue-700 hover:scale-105'}`}
          >
            {isReplying ? 'Replying...' : 'Post Reply'}
          </button>
        </div>
      </div>
    </div>
  );
};


// --- Main App Component ---
const App = () => {
  // Firebase and auth states
  const [db, setDb] = useState(null);
  const [userId, setUserId] = useState(null);
  const [isAuthReady, setIsAuthReady] = useState(false);
  const [loading, setLoading] = useState(true);

  // Form input states (for main confession post)
  const [message, setMessage] = useState('');
  const [crushName, setCrushName] = useState('');
  const [socialMedia, setSocialMedia] = useState('');
  const [isPosting, setIsPosting] = useState(false);

  // Confessions data and modal states
  const [allConfessions, setAllConfessions] = useState([]);
  const [recentConfessions, setRecentConfessions] = useState([]);
  const [archivedConfessions, setArchivedConfessions] = useState([]);
  const [filteredConfessions, setFilteredConfessions] = useState([]);
  const [showInfoModal, setShowInfoModal] = useState(false);
  const [modalMessage, setModalMessage] = useState('');
  const [isFormModalOpen, setIsFormModalOpen] = useState(false);
  const [isSearchModalOpen, setIsSearchModalOpen] = useState(false);
  const [isReplyModalOpen, setIsReplyModalOpen] = useState(false);
  const [currentConfessionToReplyTo, setCurrentConfessionToReplyTo] = useState(null);

  // Current view state ('recent', 'archive', 'popular')
  const [currentView, setCurrentView] = useState('recent'); // Default to recent

  // Search states
  const [searchTerm, setSearchTerm] = useState('');
  const [searchCategory, setSearchCategory] = useState('text');

  // Shared confession state
  const [sharedConfessionId, setSharedConfessionId] = useState(null);

  const confessionsEndRef = useRef(null);

  // Utility function to format Firestore timestamp objects
  const formatTimestamp = useCallback((ts) => {
    if (!ts) return 'A moment ago';
    if (ts.toDate) {
      return ts.toDate().toLocaleString();
    }
    return new Date(ts).toLocaleString();
  }, []);

  // Handle posting a new confession
  const handlePostConfession = useCallback(async () => {
    if (!message.trim() || !crushName.trim()) {
      setModalMessage("Please enter a message and your crush's name.");
      setShowInfoModal(true);
      return false;
    }
    if (!db || !userId) {
      setModalMessage("Cannot post: Application not fully connected. Please refresh the page.");
      setShowInfoModal(true);
      return false;
    }

    setIsPosting(true);
    try {
      const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
      const publicCollectionPath = `artifacts/${appId}/public/data/crush_board`;
      await addDoc(collection(db, publicCollectionPath), {
        message: message.trim(),
        crushName: crushName.trim(),
        socialMedia: socialMedia.trim(),
        timestamp: serverTimestamp(),
        userId: userId,
        likesCount: 0, // Initialize likes count
        likedBy: {} // Initialize an empty map to store user IDs who liked this post
      });
      setMessage('');
      setCrushName('');
      setSocialMedia('');
      setIsPosting(false);
      return true;
    } catch (error) {
      console.error("Error posting confession:", error);
      setModalMessage(`Failed to post your confession: ${error.message}.`);
      setShowInfoModal(true);
      setIsPosting(false);
      return false;
    }
  }, [db, userId, message, crushName, socialMedia]);

  // Callback for form submission (called from modal)
  const handleFormSubmit = async () => {
    const success = await handlePostConfession();
    if (success) {
      setIsFormModalOpen(false);
      setModalMessage("Your confession has been posted successfully!");
      setShowInfoModal(true);
    }
  };

  // Handle sharing a confession
  const handleShareConfession = useCallback((confessionId) => {
    const shareUrl = `${window.location.origin}${window.location.pathname}?confessionId=${confessionId}`;
    
    const textarea = document.createElement('textarea');
    textarea.value = shareUrl;
    textarea.style.position = 'fixed';
    document.body.appendChild(textarea);
    textarea.focus();
    textarea.select();
    try {
      document.execCommand('copy');
    } catch (err) {
      console.error('Failed to copy link via document.execCommand:', err);
      setModalMessage("Failed to copy link. Please copy manually: " + shareUrl);
      setShowInfoModal(true);
    }
    document.body.removeChild(textarea);
  }, []);

  // Handler to open the reply modal
  const handleOpenReplyModal = useCallback((confession) => {
    setCurrentConfessionToReplyTo(confession);
    setIsReplyModalOpen(true);
  }, []);

  // Handler to toggle a like on a confession
  const handleToggleLike = useCallback(async (confessionId, currentlyLiked) => {
    if (!db || !userId) {
      setModalMessage("Cannot like/unlike: You must be signed in.");
      setShowInfoModal(true);
      return;
    }

    const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
    const confessionRef = doc(db, `artifacts/${appId}/public/data/crush_board`, confessionId);

    try {
      if (currentlyLiked) {
        // Unlike the confession
        await setDoc(confessionRef, {
          likesCount: increment(-1),
          [`likedBy.${userId}`]: false // Mark as unliked by this user
        }, { merge: true });
      } else {
        // Like the confession
        await setDoc(confessionRef, {
          likesCount: increment(1),
          [`likedBy.${userId}`]: true // Mark as liked by this user
        }, { merge: true });
      }
    } catch (error) {
      console.error("Error toggling like:", error);
      setModalMessage(`Failed to toggle like: ${error.message}.`);
      setShowInfoModal(true);
    }
  }, [db, userId]);


  // Handler to change the main view (recent, archive, popular)
  const handleChangeView = useCallback((view) => {
    setCurrentView(view);
    setSearchTerm(''); // Clear search when changing view
    setSharedConfessionId(null); // Clear shared ID when changing view
  }, []);

  // Initialize Firebase and handle authentication
  useEffect(() => {
    try {
      const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
      const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
      const app = initializeApp(firebaseConfig);
      const firestore = getFirestore(app);
      const authInstance = getAuth(app);
      setDb(firestore);

      const params = new URLSearchParams(window.location.search);
      const idFromUrl = params.get('confessionId');
      if (idFromUrl) {
        setSharedConfessionId(idFromUrl);
        window.history.replaceState({}, document.title, window.location.pathname);
      }

      const unsub = onAuthStateChanged(authInstance, async (user) => {
        if (user) {
          setUserId(user.uid);
        } else {
          try {
            if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
              await signInWithCustomToken(authInstance, __initial_auth_token);
            } else {
              await signInAnonymously(authInstance);
            }
          } catch (error) {
            console.error("Authentication Error:", error);
            setModalMessage(`Authentication failed: ${error.message}. Please try again.`);
            setShowInfoModal(true);
          }
        }
        setIsAuthReady(true);
      });
      return () => unsub();
    } catch (error) {
      console.error("Firebase Initialization Failed:", error);
      setModalMessage(`Application initialization error: ${error.message}.`);
      setShowInfoModal(true);
      setLoading(false);
    }
  }, []);

  // Fetch confessions when Firebase is ready and distribute to recent/archive
  useEffect(() => {
    if (!db || !isAuthReady) return;
    setLoading(true);
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
    const publicCollectionPath = `artifacts/${appId}/public/data/crush_board`;
    // Fetch all documents. Client-side sorting will be applied based on currentView.
    const q = query(collection(db, publicCollectionPath)); 

    const unsub = onSnapshot(q, (snapshot) => {
      let fetched = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      
      // Ensure likesCount is a number and likedBy is an object
      fetched = fetched.map(conf => ({
        ...conf,
        likesCount: typeof conf.likesCount === 'number' ? conf.likesCount : 0,
        likedBy: typeof conf.likedBy === 'object' && conf.likedBy !== null ? conf.likedBy : {}
      }));

      // Sort by newest first for default views if not popular
      fetched.sort((a, b) => (b.timestamp?.seconds ?? 0) - (a.timestamp?.seconds ?? 0));

      const oneWeekAgo = new Date();
      oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);

      const newRecent = [];
      const newArchived = [];

      fetched.forEach(confession => {
        const confessionDate = confession.timestamp?.toDate ? confession.timestamp.toDate() : new Date(0);
        if (confessionDate > oneWeekAgo) {
          newRecent.push(confession);
        } else {
          newArchived.push(confession);
        }
      });

      setAllConfessions(fetched); // Keep all for unfiltered search
      setRecentConfessions(newRecent);
      setArchivedConfessions(newArchived);
      setLoading(false);
    }, (error) => {
      console.error("Failed to fetch confessions:", error);
      setModalMessage(`Failed to load confessions: ${error.message}`);
      setShowInfoModal(true);
      setLoading(false);
    });
    return () => unsub();
  }, [db, isAuthReady]);

  // Effect to filter and sort confessions based on shared ID, current view, and search criteria
  useEffect(() => {
    let confessionsToProcess = [];

    // Prioritize displaying a single shared confession
    if (sharedConfessionId && allConfessions.length > 0) {
      const foundConfession = allConfessions.find(c => c.id === sharedConfessionId);
      confessionsToProcess = foundConfession ? [foundConfession] : [];
    } else {
      // Determine base list based on current view
      if (currentView === 'recent') {
        confessionsToProcess = recentConfessions;
      } else if (currentView === 'archive') {
        confessionsToProcess = archivedConfessions;
      } else if (currentView === 'popular') {
        // For popular, sort all available confessions by likesCount
        // Create a shallow copy to avoid mutating state directly during sort
        confessionsToProcess = [...allConfessions].sort((a, b) => (b.likesCount || 0) - (a.likesCount || 0));
      }

      // Apply search term filtering if present
      if (searchTerm) {
        const lowerCaseSearchTerm = searchTerm.toLowerCase();
        confessionsToProcess = confessionsToProcess.filter(confession => {
          switch (searchCategory) {
            case 'text':
              return (
                confession.message.toLowerCase().includes(lowerCaseSearchTerm) ||
                confession.crushName.toLowerCase().includes(lowerCaseSearchTerm)
              );
            case 'date':
              const formattedDate = formatTimestamp(confession.timestamp);
              return formattedDate.toLowerCase().includes(lowerCaseSearchTerm);
            case 'id':
              return confession.id.toLowerCase() === lowerCaseSearchTerm;
            case 'crushName':
              return confession.crushName.toLowerCase().includes(lowerCaseSearchTerm);
            default:
              return false;
          }
        });
      }
    }
    setFilteredConfessions(confessionsToProcess);
  }, [allConfessions, recentConfessions, archivedConfessions, searchTerm, searchCategory, currentView, sharedConfessionId, formatTimestamp]);

  // Function to clear the shared confession view
  const handleClearSharedConfession = useCallback(() => {
    setSharedConfessionId(null);
    window.history.replaceState({}, document.title, window.location.pathname);
  }, []);

  // Display loading spinner until authentication is ready
  if (!isAuthReady) {
    return <div className="flex items-center justify-center min-h-screen bg-gradient-to-br from-pink-300 via-purple-300 to-indigo-400"><LoadingSpinner /></div>;
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-pink-300 via-purple-300 to-indigo-400 p-4 font-inter text-gray-800 flex flex-col items-center justify-between">
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .custom-scrollbar::-webkit-scrollbar { width: 8px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: rgba(139, 92, 246, 0.5); border-radius: 10px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: rgba(139, 92, 246, 0.8); }
        @keyframes fade-in-up { 0% { opacity: 0; transform: translateY(20px); } 100% { opacity: 1; transform: translateY(0); } }
        .animate-fade-in-up { animation: fade-in-up 0.5s ease-out forwards; }
        @keyframes modal-in { 0% { opacity: 0; transform: scale(0.9); } 100% { opacity: 1; transform: scale(1); } }
        .animate-modal-in { animation: modal-in 0.3s ease-out forwards; }
      `}</style>
      
      {/* Navigation Bar at the top */}
      <NavBar
        onOpenSearch={() => { setIsSearchModalOpen(true); setSearchTerm(''); }}
        onChangeView={handleChangeView}
        currentView={currentView}
      />

      {/* Main Header */}
      <Header
        userId={userId}
        onClearSharedConfession={sharedConfessionId ? handleClearSharedConfession : null}
      />

      {/* Main Confessions List (Recent, Archived, Popular, or Single Shared) */}
      <ConfessionsList
        loading={loading}
        confessions={filteredConfessions}
        formatTimestamp={formatTimestamp}
        confessionsEndRef={confessionsEndRef}
        title={sharedConfessionId ? "Shared Confession" : (currentView === 'recent' ? "Recent Confessions" : (currentView === 'archive' ? "Archived Confessions" : "Popular Confessions"))}
        onShare={handleShareConfession}
        onOpenReplyModal={handleOpenReplyModal}
        onToggleLike={handleToggleLike}
        userId={userId}
      />

      {/* Floating Action Button for Posting Confessions */}
      <div className="fixed bottom-6 right-6 z-40">
        <button onClick={() => setIsFormModalOpen(true)} className="bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-700 hover:to-pink-700 text-white p-4 rounded-full shadow-lg transition duration-300 ease-in-out transform hover:scale-110 hover:shadow-2xl" aria-label="Post a new confession" title="Post a new confession">
          <PlusIcon />
        </button>
      </div>

      {/* Confession Form Modal */}
      <ConfessionFormModal isOpen={isFormModalOpen} onClose={() => setIsFormModalOpen(false)}>
        <h2 className="text-2xl font-semibold text-gray-800 mb-4 text-center">Post Your Confession</h2>
        <div className="space-y-4">
          <textarea
            className="w-full p-3 bg-white/80 border border-gray-300/50 rounded-lg focus:ring-2 focus:ring-purple-400 focus:border-transparent transition duration-200 resize-y min-h-[100px] placeholder-gray-500"
            placeholder="Your secret message..."
            value={message}
            onChange={(e) => setMessage(e.target.value)}
            rows="4"
          />
          <input
            type="text"
            className="w-full p-3 bg-white/80 border border-gray-300/50 rounded-lg focus:ring-2 focus:ring-purple-400 focus:border-transparent transition duration-200 placeholder-gray-500"
            placeholder="Your crush's name or a hint..."
            value={crushName}
            onChange={(e) => setCrushName(e.target.value)}
          />
          <input
            type="text"
            className="w-full p-3 bg-white/80 border border-gray-300/50 rounded-lg focus:ring-2 focus:ring-pink-400 focus:border-transparent transition duration-200 placeholder-gray-500"
            placeholder="Social media (optional, e.g., @username or link)"
            value={socialMedia}
            onChange={(e) => setSocialMedia(e.target.value)}
          />
          <button
            onClick={handleFormSubmit}
            disabled={isPosting}
            className={`w-full py-3 px-6 rounded-full font-bold text-white shadow-lg transform transition duration-300 ease-in-out ${isPosting ? 'bg-gray-400 cursor-not-allowed' : 'bg-gradient-to-r from-purple-600 to-pink-600 hover:scale-105 hover:shadow-xl'}`}
          >
            {isPosting ? 'Posting...' : '🚀 Post My Confession'}
          </button>
        </div>
      </ConfessionFormModal>

      {/* Search Modal */}
      <SearchModal
        isOpen={isSearchModalOpen}
        onClose={() => setIsSearchModalOpen(false)}
        searchTerm={searchTerm}
        setSearchTerm={setSearchTerm}
        searchCategory={searchCategory}
        setSearchCategory={setSearchCategory}
      />

      {/* Reply Modal */}
      <ReplyModal
        isOpen={isReplyModalOpen}
        onClose={() => { setIsReplyModalOpen(false); setCurrentConfessionToReplyTo(null); }}
        originalConfession={currentConfessionToReplyTo}
        db={db}
        userId={userId}
        formatTimestamp={formatTimestamp}
        onShowInfoModal={(msg) => { setModalMessage(msg); setShowInfoModal(true); }}
      />

      {/* Info/Error Modal */}
      {showInfoModal && <InfoModal message={modalMessage} onClose={() => setShowInfoModal(false)} />}

      {/* Slim Footer */}
      <footer className="w-full max-w-3xl bg-white/30 backdrop-blur-lg p-3 rounded-2xl shadow-xl mt-8 flex flex-col items-center justify-center text-gray-700 text-sm border border-white/20">
        <p className="mb-1">©️AVSC 2025 | Built for projectbeta webdev bootcamp</p>
        <p>Powered by ASTRA, Hackclub and the music of love</p>
      </footer>
    </div>
  );
};

export default App;
