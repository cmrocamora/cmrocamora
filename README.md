import React, { useState, useEffect, useRef } from 'react';
import ReactDOM from 'react-dom/client';
import { Y, YArray, YText, YMap } from 'yjs';
import { WebrtcProvider } from 'y-webrtc';
import { WebsocketProvider } from 'y-websocket';

const App = () => {
  const [yDoc, setYDoc] = useState(null);
  const textRef = useRef(null);

  useEffect(() => {
    // Create a Yjs document
    const ydoc = new Y(
      new WebsocketProvider('ws://your-server-address'),
      new WebrtcProvider('your-room-name') 
    );
    setYDoc(ydoc);

    // Create a YText instance (for the document content)
    const ytext = ydoc.get('text', YText);

    // Update the text area with Yjs changes
    ytext.observe(() => {
      if (textRef.current) {
        textRef.current.value = ytext.toString();
      }
    });
  }, []);

  const handleChange = (event) => {
    // Update Yjs with changes from the text area
    if (yDoc) {
      const ytext = yDoc.get('text', YText);
      ytext.delete(0, ytext.length);
      ytext.insert(0, event.target.value);
    }
  };

  return (
    <div>
      <h1>Collaborative Text Editor</h1>
      <textarea ref={textRef} onChange={handleChange} />
    </div>
  );
};

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
